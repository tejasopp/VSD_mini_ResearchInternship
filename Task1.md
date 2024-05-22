# Writing a C code to count the sum of numbers from 1 to n and running this program using RISC-V simulator

## Writing a C code to count sum of numbers from 1 to n
1. First I opened terinal and wrote code to make and open a new C file in leafpad as shown below:

![Screenshot 2024-05-22 110523](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/12319c96-5cbd-4198-825c-d67daa092d36)

2. Then I wrote my code in leafpad as shown below:

![Screenshot 2024-05-22 110552](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/e0261b81-3dc8-47b2-8402-76403c908d38)

3. And then compiled and run in terminal itself to check, and got the desired output.

![Screenshot 2024-05-22 110646](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/f4417f84-38ed-4462-92ca-e78034b1d88a)

4. Tried for different value of n and got the results, like n=100.

![Screenshot 2024-05-22 110914](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/8e717e17-5470-48c0-acf5-88448b4efcd4)

## Running above program in RISC-V Simulator
Once I have ran the code in terminal, I need to run in RISC-V simulator so for that their is special set of code needed which I have done in following steps:

1. First I wrote a code to compile it with RISC-V gcc compiler with option 1 so it will generate a file with .o extension.

![Screenshot 2024-05-22 113523](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/79995c78-3995-4a0a-b981-c26fb25ac48d)

2. Next I wrote a code ***riscv-unknown-elf-objdump -d sum1ton.o*** for getting the assembly code for the above c program, and got many assembly codes. Reduced the number of assembly codes just by writing ***| less*** after the command.
   We wanted **main** section so I searched for the main. The byte address for main was found to be 10184 and got 15 instrusctions when using option 1. It is observed that the address of each consecutive instructions get incremented by 4.

![Screenshot 2024-05-22 113339](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/7a0987ad-7384-4e02-a330-39db9d90eeb2)

3. Next I run the same commands but with different option, instead of ***O1***, I used ***Ofast***. This time I got less number of instructions i.e. 12. This shows that the instrunctions in assembly codes of the file changes for different options of compilation.

![Screenshot 2024-05-22 113733](https://github.com/tejasopp/VSD_mini_ResearchInternship/assets/117591903/f260c3fb-3790-4bbb-bdc6-ccd41314049d)

### That is it for Task1!

