# Project : CH32V003 RISC-V Mini Game Console
## Overview
The Handheld Gaming Console project integrates a RISC-V microcontroller to create a portable gaming system featuring a OLED display and tactile buttons. This console allows users to play classic games like Snake and Pong, with the RISC-V microcontroller managing game logic, user input, and rendering graphics. Additionally, a sound module provides audio feedback to enhance the gaming experience. This project demonstrates the innovative use of RISC-V technology in creating an immersive and interactive gaming solution.
![GameConsole_pic1](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/e3e6c984-1eec-407f-b5c5-ea7b3ac366c7)

## Components Required
1. VSDSquadron Mini
2. Oled Display
3. Push buttons (5)
4. Capacitors (100nF, 10uF)
5. Coin Battery (	CR2032)
6. Resistors (1k, 2k, 3k, 20k, 8k)
7. Buzzer (MLT-5030)
8. Hreader pins
9. BJT (	MMBT3906)
10. Switch


## Circuit Connections
The VSDSquadron Mini RISC-V development boards features a RISC-V SoC with the following capabilities:

- On-board 24MHz RC oscillator
-  3 groups of GPIO ports, totaling 15 I/O ports
- USART, I2C, and SPI
- UART implemented on USART
- 2KB SRAM for volatile data storage, 16KB CodeFlash for program memory
- On-board Programmer.

The circuit connections are as follows:
- **PA2** GPIO Pin is connected to one of the push bottons which controls the turn on/off of a game.
- **PA1** is connected to the buzzer.
- **PC4** is connected to the game controlling push buttons
- **PC2** and **PC1** act as SCL(Serial Clock Line) and SDL(Seriel Data Line), these lines are used for I2C communication with OLED Display
- And there are some VCC and GND connections.
- Power switch connected to VCC and GND accordingly.

  ![GameConsole_wiring](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/1635d8a1-8faa-4052-a98d-c688ece05d02)

  ### Table for Pin connections

![image](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/93ad1ecf-48d7-44a6-ac2c-c30f88f1a42a)

## My Circuit:
![Presentation1](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/abd1aca4-ba3a-471b-b7e2-3f2929d15c42)

## Software
I made one of my most played games of my childhood which is **PACMAN**.

My code:
```
#include "driver.h"
#include "spritebank.h"

// ===================================================================================
// Global Variables
// ===================================================================================
uint8_t LEVELSPEED;
uint8_t GobbingEND;
uint8_t LIVE;
uint8_t INGAME;
uint8_t Gobeactive;
uint8_t TimerGobeactive;
uint8_t add;
uint8_t dotsMem[9];
int8_t dotscount;
uint8_t Frame;
enum {PACMAN=0,FANTOME=1,FRUIT=2};

// ===================================================================================
// Function Prototypes
// ===================================================================================
void ResetVar(void);
void StartGame(PERSONAGE *Sprite);
uint8_t CollisionPac2Caracter(PERSONAGE *Sprite);
void RefreshCaracter(PERSONAGE *Sprite);
uint8_t CheckCollisionWithBack(uint8_t SpriteCheck,uint8_t HorVcheck,PERSONAGE *Sprite);
uint8_t RecupeBacktoCompV(uint8_t SpriteCheck,PERSONAGE *Sprite);
uint8_t Trim(uint8_t Y1orY2,uint8_t TrimValue,uint8_t Decalage);
uint8_t RecupeBacktoCompH(uint8_t SpriteCheck,PERSONAGE *Sprite);
void Tiny_Flip(uint8_t render0_picture1,PERSONAGE *Sprite);
uint8_t FruitWrite(uint8_t x,uint8_t y);
uint8_t LiveWrite(uint8_t x,uint8_t y);
uint8_t DotsWrite(uint8_t x,uint8_t y,PERSONAGE *Sprite);
uint8_t checkDotPresent(uint8_t  DotsNumber);
void DotsDestroy(uint8_t DotsNumber);
uint8_t SplitSpriteDecalageY(uint8_t decalage,uint8_t Input,uint8_t UPorDOWN);
uint8_t SpriteWrite(uint8_t x,uint8_t y,PERSONAGE  *Sprite);
uint8_t return_if_sprite_present(uint8_t x,PERSONAGE  *Sprite,uint8_t SpriteNumber);
uint8_t background(uint8_t x,uint8_t y);

// ===================================================================================
// Main Function
// ===================================================================================
int main(void) {
  // Setup
  JOY_init();

  // Loop
  while(1) {
    uint8_t t;
    PERSONAGE Sprite[5];
  NEWGAME:
    ResetVar();
    LIVE=3;
    goto New;
  NEWLEVEL:
    if(LEVELSPEED > 10) {
      LEVELSPEED=LEVELSPEED - 10;
      if((LEVELSPEED==160)||(LEVELSPEED==120)||(LEVELSPEED==80)||(LEVELSPEED==40)||(LEVELSPEED==10)) {    
        if(LIVE < 3) {
          LIVE++; 
          for(t=0; t<=4; t++) {
            JOY_sound(80,100);
            JOY_DLY_ms(300);
          }
        }
      }
    }
  New:
    GobbingEND = (LEVELSPEED / 2);
    for(t=0; t<9; t++) dotsMem[t]=0xff;
  RESTARTLEVEL:
    Gobeactive = 0;
    uint8_t* ptr = (uint8_t*)Sprite;
    for(t=5*10; t; t--) *ptr++ = 0;
    Sprite[0].type=PACMAN;
    Sprite[0].x=64;
    Sprite[0].y=3;
    Sprite[0].Decalagey=5;
    Sprite[0].DirectionV=2;
    Sprite[0].DirectionH=2;
    Sprite[0].DirectionAnim=0;
    Sprite[1].type=FANTOME;
    Sprite[1].x=76;
    Sprite[1].y=4;
    Sprite[1].guber=0;
    Sprite[2].type=FANTOME;
    Sprite[2].x=75;
    Sprite[2].y=5;
    Sprite[2].guber=0;
    Sprite[3].type=FANTOME;
    Sprite[3].x=77;
    Sprite[3].y=4;
    Sprite[3].guber=0;
    Sprite[4].type=FANTOME;
    Sprite[4].x=76;
    Sprite[4].y=5;
    Sprite[4].guber=0;
    while(1) {
      //joystick
      if(JOY_act_pressed()) StartGame(&Sprite[0]);
      if(INGAME) {
        if(JOY_left_pressed()) Sprite[0].DirectionV = 0;
        else if(JOY_right_pressed()) Sprite[0].DirectionV = 1;
        if(JOY_down_pressed()) Sprite[0].DirectionH =1 ;
        else if(JOY_up_pressed()) Sprite[0].DirectionH = 0;
        //fin joystick
        if(TimerGobeactive > 1) TimerGobeactive--;
        else if (TimerGobeactive == 1) {
          TimerGobeactive = 0;
          Gobeactive = 0;
        }
      }
      if(Frame < 24) Frame++;
      else Frame = 0;
      if(CollisionPac2Caracter(&Sprite[0]) == 0) RefreshCaracter(&Sprite[0]);
      else {
        JOY_sound(100, 200); JOY_sound(75, 200); JOY_sound(50, 200); JOY_sound(25, 200);
        JOY_sound(12, 200); JOY_DLY_ms(400);
        if(LIVE > 0) {
          LIVE--;
          goto RESTARTLEVEL;
        }
        else goto NEWGAME;
      }
      if(Frame % 2 == 0) {
        Tiny_Flip(0, &Sprite[0]);
        if(INGAME == 1) {
          for(uint8_t t=0; t<=139; t=t+2) {
            JOY_sound((Music[t]) - 8, ((Music[t + 1]) - 100)); 
          }
          INGAME = 2;
        }
      }
      else {
        for(t=0; t<63; t++) {
          if(checkDotPresent(t)) break;
          else if(t == 62) {
            for(uint8_t r=0; r<60; r++) {
              JOY_sound(2 + r, 10); JOY_sound(255 - r, 20);
            }
            JOY_DLY_ms(1000);
            goto NEWLEVEL;
          }
        }
      }
      if((Gobeactive) && (Frame % 2 == 0)) JOY_sound((255 - TimerGobeactive), 1);
      JOY_SLOWDOWN();
    }
  }
}

// ===================================================================================
// Functions
// ===================================================================================
void ResetVar(void){
LEVELSPEED=200;
GobbingEND=0;
LIVE=3;
Gobeactive=0;
TimerGobeactive=0;
add=0;
INGAME=0;
for(uint8_t t=0;t<9;t++){
dotsMem[t]=0xff;}
dotscount=0;
Frame=0;}

void StartGame(PERSONAGE *Sprite){
if (INGAME==0) {
Sprite[1].x=76;
Sprite[1].y=3;
Sprite[2].x=75;
Sprite[2].y=4;
Sprite[3].x=77;
Sprite[3].y=3;
Sprite[4].x=76;
Sprite[4].y=4;
INGAME=1;}}

uint8_t CollisionPac2Caracter(PERSONAGE *Sprite){
uint8_t ReturnCollision=0;
#define xmax(I) (Sprite[I].x+6)
#define xmin(I) (Sprite[I].x)
#define ymax(I) ((Sprite[I].y*8)+Sprite[I].Decalagey+6)
#define ymin(I) ((Sprite[I].y*8)+Sprite[I].Decalagey)
if ((INGAME)) {    
for (uint8_t t=1;t<=4;t++){
if ((xmax(0)<xmin(t))||(xmin(0)>xmax(t))||(ymax(0)<ymin(t))||(ymin(0)>ymax(t))) {}else{ 
if (Gobeactive) {if (Sprite[t].guber!=1) {JOY_sound(20,100);JOY_sound(2,100);}Sprite[t].guber=1;ReturnCollision=0;}else{ if (Sprite[t].guber==1) {ReturnCollision=0;}else{ReturnCollision=1;}}
}}}return ReturnCollision;}

void RefreshCaracter(PERSONAGE *Sprite){
uint8_t memx,memy,memdecalagey;
for (uint8_t t=0;t<=4;t++){
memx=Sprite[t].x;
memy=Sprite[t].y;
memdecalagey=Sprite[t].Decalagey;
if ((Sprite[t].y>-1)&&(Sprite[t].y<8)) {
if ((Frame%2==0)||(t==0)||(LEVELSPEED<=160)) {
if (Sprite[t].DirectionV==1) {Sprite[t].x++;}
if (Sprite[t].DirectionV==0) {
if (t==0) {
if ((Sprite[0].y==3)&&(Sprite[0].x==86)){}else{Sprite[t].x--;}
}else{Sprite[t].x--;}
}}}
if (CheckCollisionWithBack(t,1,Sprite)) {
if (t!=0) {Sprite[t].DirectionV=JOY_random()%2;}else{ Sprite[t].DirectionV=2;}
Sprite[t].x=memx;
}
if ((Frame%2==0)||(t==0)||(LEVELSPEED<=160)) {
if (Sprite[t].DirectionH==1) {if (Sprite[t].Decalagey<7) {Sprite[t].Decalagey++;}else{Sprite[t].Decalagey=0;Sprite[t].y++;if (Sprite[t].y==9) {Sprite[t].y=-1;}}}
if (Sprite[t].DirectionH==0) {if (Sprite[t].Decalagey>0) {Sprite[t].Decalagey--;}else{Sprite[t].Decalagey=7;Sprite[t].y--;if (Sprite[t].y==-2) {Sprite[t].y=8;}}}
}
if (CheckCollisionWithBack(t,0,Sprite)) {
if (t!=0) {Sprite[t].DirectionH=JOY_random()%2;}else{Sprite[t].DirectionH=2;}
Sprite[t].y=memy;
Sprite[t].Decalagey=memdecalagey;
}
if (t==0) {
if (Frame%2==0) {
if (Sprite[t].DirectionH==1) {Sprite[t].DirectionAnim=0;}
if (Sprite[t].DirectionH==0) {Sprite[t].DirectionAnim=(2*3);} 
if (Sprite[t].DirectionV==1) {Sprite[t].DirectionAnim=(3*3);}
if (Sprite[t].DirectionV==0) {Sprite[t].DirectionAnim=(1*3);}
}}else{
if ((Frame==0)||(Frame==12)) {
Sprite[t].DirectionAnim=0;
if (Sprite[t].DirectionH==1) {Sprite[t].DirectionAnim=0;}
if (Sprite[t].DirectionH==0) {Sprite[t].DirectionAnim=2;}
}}
if (t==0) {
if (Frame%2==0) {
if (Sprite[0].switchanim==0) {
if (Sprite[0].anim<2) {Sprite[0].anim++;}else{Sprite[0].switchanim=1;} 
}else{
if (Sprite[0].anim>0) {Sprite[0].anim--;}else{Sprite[0].switchanim=0;}  
}}}else{
if ((Sprite[t].guber==1)&&(Sprite[t].x>=74)&&(Sprite[t].x<=76)&&(Sprite[t].y>=2)&&(Sprite[t].y<=4)) {Sprite[t].guber=0;}
if  (Frame%2==0) {
if (Sprite[t].anim<1) {Sprite[t].anim++;}else{Sprite[t].anim=0; } 
}}}}

uint8_t CheckCollisionWithBack(uint8_t SpriteCheck,uint8_t HorVcheck,PERSONAGE *Sprite){
uint8_t BacktoComp;
if (HorVcheck==1) {
BacktoComp=RecupeBacktoCompV(SpriteCheck,Sprite); 
}else{
BacktoComp=RecupeBacktoCompH(SpriteCheck,Sprite);}
if ((BacktoComp)!=0) {return 1;}else{return 0;}}

uint8_t RecupeBacktoCompV(uint8_t SpriteCheck,PERSONAGE *Sprite){
uint8_t Y1=0b00000000;
uint8_t Y2=Y1;
#define SpriteWide 6
#define MAXV (Sprite[SpriteCheck].x+SpriteWide)
#define MINV (Sprite[SpriteCheck].x)
if (Sprite[SpriteCheck].DirectionV==1) {
Y1=(back[((Sprite[SpriteCheck].y)*128)+(MAXV)]);
Y2=(back[((Sprite[SpriteCheck].y+1)*128)+(MAXV)]);
}else if (Sprite[SpriteCheck].DirectionV==0) {
Y1=(back[((Sprite[SpriteCheck].y)*128)+(MINV)]);
Y2=(back[((Sprite[SpriteCheck].y+1)*128)+(MINV)]);
}else{Y1=0;Y2=0;}
//decortique
Y1=Trim(0,Y1,Sprite[SpriteCheck].Decalagey);
Y2=Trim(1,Y2,Sprite[SpriteCheck].Decalagey);
if (((Y1)!=0b00000000)||((Y2)!=0b00000000) ) {return 1;}else{return 0;}
}

uint8_t Trim(uint8_t Y1orY2,uint8_t TrimValue,uint8_t Decalage){
uint8_t Comp;
if (Y1orY2==0) {
Comp=0b01111111<<Decalage;
return (TrimValue&Comp);
}else{
Comp=(0b01111111>>(8-Decalage));
return (TrimValue&Comp);
}}

uint8_t ScanHRecupe(uint8_t UporDown,uint8_t Decalage){
if (UporDown==0){
return 0b01111111<<Decalage;}
else{
return 0b01111111>>(8-Decalage);
}}

uint8_t RecupeBacktoCompH(uint8_t SpriteCheck,PERSONAGE *Sprite){
uint8_t TempPGMByte;
if (Sprite[SpriteCheck].DirectionH==0) {
uint8_t RECUPE=(ScanHRecupe(0,Sprite[SpriteCheck].Decalagey));
for(uint8_t t=0;t<=6;t++){
if ((((Sprite[SpriteCheck].y)*128)+(Sprite[SpriteCheck].x+t)>1023)||(((Sprite[SpriteCheck].y)*128)+(Sprite[SpriteCheck].x+t)<0)) {TempPGMByte=0x00;}else{
 TempPGMByte=((back[((Sprite[SpriteCheck].y)*128)+(Sprite[SpriteCheck].x+t)])); 
}
#define CHECKCOLLISION ((RECUPE)&(TempPGMByte))
if  (CHECKCOLLISION!=0) {return 1;}
}}else if (Sprite[SpriteCheck].DirectionH==1) {
uint8_t tadd=0;
if (Sprite[SpriteCheck].Decalagey>2) { tadd=1;}else{tadd=0;}
uint8_t RECUPE=(ScanHRecupe(tadd,Sprite[SpriteCheck].Decalagey));
for(uint8_t t=0;t<=6;t++){
if (((((Sprite[SpriteCheck].y+tadd)*128)+(Sprite[SpriteCheck].x+t))>1023)||((((Sprite[SpriteCheck].y+tadd)*128)+(Sprite[SpriteCheck].x+t))<0)) {TempPGMByte=0x00;}else{
TempPGMByte=(back[((Sprite[SpriteCheck].y+tadd)*128)+(Sprite[SpriteCheck].x+t)]);
}
#define CHECKCOLLISION2 ((RECUPE)&(TempPGMByte))
if  (CHECKCOLLISION2!=0) {return 1;}
}}return 0;}

void Tiny_Flip(uint8_t render0_picture1,PERSONAGE *Sprite){
uint8_t y,x; 
dotscount=-1;
for (y = 0; y < 8; y++){ 
JOY_OLED_data_start(y);
for (x = 0; x < 128; x++){
if (render0_picture1==0) {
if (INGAME) {JOY_OLED_send(background(x,y)|SpriteWrite(x,y,Sprite)|DotsWrite(x,y,Sprite)|LiveWrite(x,y)|FruitWrite(x,y));}else{
JOY_OLED_send(0xff-(background(x,y)|SpriteWrite(x,y,Sprite)));
}}else if (render0_picture1==1){
JOY_OLED_send((back[x+(y*128)]));}}
JOY_OLED_end();
}}

uint8_t FruitWrite(uint8_t x,uint8_t y){
switch(y){
  case 7:if (x<=7) {return (fruits[x]);}break;
  case 6:if ((LEVELSPEED<=190)&&(x<=7)) {return (fruits[x+8]);}break;
  case 5:if ((LEVELSPEED<=180)&&(x<=7)) {return (fruits[x+16]);}break;
  case 4:if ((LEVELSPEED<=170)&&(x<=7)) {return (fruits[x+24]);}break;
}return 0;}

uint8_t LiveWrite(uint8_t x,uint8_t y){
if (y<LIVE) {if (x<=7) {return (caracters[x+(1*8)]);}else{return 0;}
}return 0x00;}

uint8_t DotsWrite(uint8_t x,uint8_t y,PERSONAGE *Sprite){
uint8_t Menreturn=0;
uint8_t mem1=(dots[x+(128*y)]);
if (mem1!=0b00000000) {
dotscount++;
 switch(dotscount){
  case 0:  
  case 1: 
  case 12: 
  case 13: 
  case 50:
  case 51:
  case 62:
  case 63:Menreturn=1;break;
  default:Menreturn=0;break;
}
if (checkDotPresent(dotscount)==0b00000000) {mem1=0x00;}else{
if ((Sprite[0].type==PACMAN)&&((Sprite[0].x<x)&&(Sprite[0].x>x-6))&&(((Sprite[0].y==y)&&(Sprite[0].Decalagey<6))||((Sprite[0].y==y-1)&&(Sprite[0].Decalagey>5)))) {DotsDestroy(dotscount);if (Menreturn==1) {TimerGobeactive=LEVELSPEED;Gobeactive=1;}else{JOY_sound(10,10);JOY_sound(50,10);}}
}}
if (Menreturn==1) {
if (((Frame>=6)&&(Frame<=12))||((Frame>=18)&&(Frame<=24))) {
return 0x00;
}else{return mem1;}
}else{return mem1;}}

uint8_t checkDotPresent(uint8_t  DotsNumber){
uint8_t REST=DotsNumber;
uint8_t DOTBOOLPOSITION=0;
DECREASE:
if (REST>=8) {REST=REST-8;DOTBOOLPOSITION++;goto DECREASE;}
return ((dotsMem[DOTBOOLPOSITION])&(0b10000000>>REST));
}

void DotsDestroy(uint8_t DotsNumber){
uint8_t REST=DotsNumber;
uint8_t DOTBOOLPOSITION=0;
uint8_t SOUSTRAIRE;
DECREASE:
if (REST>=8) {REST=REST-8;DOTBOOLPOSITION=DOTBOOLPOSITION+1;goto DECREASE;}
switch(REST){
  case (0):SOUSTRAIRE=0b01111111;break;
  case (1):SOUSTRAIRE=0b10111111;break;
  case (2):SOUSTRAIRE=0b11011111;break;
  case (3):SOUSTRAIRE=0b11101111;break;
  case (4):SOUSTRAIRE=0b11110111;break;
  case (5):SOUSTRAIRE=0b11111011;break;
  case (6):SOUSTRAIRE=0b11111101;break;
  case (7):SOUSTRAIRE=0b11111110;break;
}
dotsMem[DOTBOOLPOSITION]=dotsMem[DOTBOOLPOSITION]&SOUSTRAIRE;
}

uint8_t SplitSpriteDecalageY(uint8_t decalage,uint8_t Input,uint8_t UPorDOWN){
if (UPorDOWN) {
return Input<<decalage;
}else{
return Input>>(8-decalage); 
}}

uint8_t SpriteWrite(uint8_t x,uint8_t y,PERSONAGE  *Sprite){
uint8_t var1=0;
uint8_t AddBin=0b00000000;
while(1){ 
if (Sprite[var1].y==y) {
AddBin=AddBin|SplitSpriteDecalageY(Sprite[var1].Decalagey,return_if_sprite_present(x,Sprite,var1),1);
}else if (((Sprite[var1].y+1)==y)&&(Sprite[var1].Decalagey!=0)) {
AddBin=AddBin|SplitSpriteDecalageY(Sprite[var1].Decalagey,return_if_sprite_present(x,Sprite,var1),0);
}
var1++;
if (var1==5) {break;}
}return AddBin;}
  
uint8_t return_if_sprite_present(uint8_t x,PERSONAGE  *Sprite,uint8_t SpriteNumber){
uint8_t ADDgobActive;
uint8_t ADDGober;
if  ((x>=Sprite[SpriteNumber].x)&&(x<(Sprite[SpriteNumber].x+8))) { 
if (SpriteNumber!=0) { 
if (Sprite[SpriteNumber].guber==1) {
ADDgobActive=1*(4*8);
ADDGober=Sprite[SpriteNumber].guber*(4*8);
}else{
if ((((Frame>=6)&&(Frame<=12))||((Frame>=18)&&(Frame<=24)))||(TimerGobeactive>GobbingEND)) {ADDgobActive=Gobeactive*(4*8);}else{ADDgobActive=0;}
ADDGober=0;
}}else{
ADDGober=0;
ADDgobActive=0;
}
if ((INGAME==0)&&(SpriteNumber==0)) {  return 0;}     
return (caracters[((x-Sprite[SpriteNumber].x)+(8*(Sprite[SpriteNumber].type*12)))+(Sprite[SpriteNumber].anim*8)+(Sprite[SpriteNumber].DirectionAnim*8)+(ADDgobActive)+(ADDGober)]);
}return 0;}

uint8_t background(uint8_t x,uint8_t y){
return (BackBlitz[((y)*128)+((x))]);
}
```
## Results:
### My game video link : https://drive.google.com/file/d/1Wr2tZUExrr649TMdLOXeVw5DllF4_PgC/view?usp=drivesdk

![kkmelon](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/69d460a2-4968-4567-bde5-605b55ef101c)





