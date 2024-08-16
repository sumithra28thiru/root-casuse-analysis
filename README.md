Root Cause Analysis Chatbot Tool
This CLI-based Root Cause Analysis (RCA) chatbot is built using TypeScript in a Node.js environment. It leverages OpenAI's API to analyze stack trace files from software crashes and determine their root causes.

Abstract
Since late 2022 and early 2023, Large Language Models (LLMs), notably OpenAI's ChatGPT, have gained significant traction. Despite ongoing discussions about their effectiveness, research, and applications, LLMs are increasingly utilized in various domains. This capstone project explores whether an RCA tool for software crashes, powered by an LLM, can offer a practical alternative to existing RCA methods. The project evaluates the LLM-based RCA tool against traditional methods using two metrics: the accuracy of analysis results with minimal crash data and the ease of understanding of the tool’s responses.

Keywords: large language model (LLM), root cause analysis, chatbots, OpenAI, ChatGPT, security testing, software testing, software crashes

Pre-requisites
Node.js
To run this chatbot, Node.js is required. The tool was developed using Node v20.10.0 on macOS Sonoma 14.3.1.

Verify your Node.js installation with:

shell
Copy code
node -v
TypeScript
For optimal performance, TypeScript is necessary. The package.json file lists TypeScript as a project dependency, which you can install using npm i.

This project was developed with TypeScript 5.3.3. Check your TypeScript version with:

shell
Copy code
tsc -v
Setup
Obtain an OpenAI API key from OpenAI's API key page.

Create a file named .env and include:

makefile
Copy code
API_KEY=<YOUR_API_KEY_HERE>
Replace <YOUR_API_KEY_HERE> with your OpenAI API key.

The project folder structure should look like this:

scss
Copy code
rca-tool/
|- crashes         (contains stack trace files for analysis)
|- dist/           (JavaScript files generated from TypeScript)
|- node_modules/   (installed dependencies)
|- samples/        (full stack trace outputs from fuzzing various CVEs)
|- src/            (TypeScript source code)
   |- components/
      |- functions.ts      (functions based on user prompts)
      |- index.ts
      |- lib/
         |- getNewUserPrompt.ts
         |- getReadLimit.ts
         |- index.ts
         |- saveSession.ts
   |- config/
      |- index.ts
      |- open-ai.ts
   |- index.ts
|- sessions/      (JSON logs of previous session prompts and responses)
|- tests/         (unit tests for functions)
   |- functions.test.ts
   |- getReadLimit.test.ts
|- .env           (create this file)
|- .gitignore
|- .prettierrc.json
|- chat-history.json
|- package.json
|- README.md
|- tsconfig.json
|- vitest.config.json
Install dependencies:

shell
Copy code
npm i
Run the chatbot:

shell
Copy code
npm run ce
This command compiles the TypeScript code from src/ into JavaScript files in dist/ and then executes them. Alternatively, compile and run the code in two steps:

shell
Copy code
tsc # Compile TypeScript
node dist/index.js # Run the compiled JavaScript
You can also directly run the chatbot if the JavaScript files are already compiled:

shell
Copy code
node dist/index.js
Usage
Ensure the crashes folder contains text files with stack trace data before running the application. An example stack trace might look like:

arduino
Copy code
==11852==ERROR: AddressSanitizer: heap-buffer-overflow on address 0x602000003033 at pc 0x556321736838 bp 0x7ffe4d212270 sp 0x7ffe4d212260
WRITE of size 1 at 0x602000003033 thread T0
    #0 0x556321736837 in quote_for_pmake asm/nasm.c:856
    #1 0x556321736837 in quote_for_pmake asm/nasm.c:784
    #2 0x556321732055 in emit_dependencies asm/nasm.c:397
    #3 0x556321732055 in main asm/nasm.c:738
    #4 0x7f1d84829d8f  (/lib/x86_64-linux-gnu/libc.so.6+0x29d8f)
    #5 0x7f1d84829e3f in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x29e3f)
    #6 0x556321734c6d in _start (/home/deezombiedude/rca/13_nasm_2022-44370/nasm_fuzz+0x11bc6d)
When prompted, specify the number of stack trace files to analyze. The limit is determined by the token limit (currently 10,000). The order of file analysis is alphabetical.

After receiving the response, you can:

Follow-up: Type your follow-up question as you would in any chatbot interaction.
Reanalyze: Enter readCrashes to analyze stack traces again.
Quit: Enter quit or exit.
Notes:

Avoid using CTRL+c to terminate the session, as it may not corrupt files but is discouraged.
Current caveat (as of Feb 21, 2024): The number of stack trace files that can be processed is limited by token constraints.
tsconfig.json
Ensure your tsconfig.json is configured as follows:

json
Copy code
{
  "compilerOptions": {
    "target": "es2016",
    "module": "commonjs",
    "rootDir": "./src",
    "moduleResolution": "node10",
    "sourceMap": true,
    "outDir": "./dist",
    "removeComments": true,
    "noEmitOnError": true,
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "strict": true,
    "noImplicitReturns": true,
    "noImplicitOverride": true,
    "allowUnreachableCode": true,
    "skipLibCheck": true
  },
  "exclude": [
    "tests/**/*",
    "**/*.test.ts"
  ]
}
Stack Trace Output Sample Pool
The following CVEs were used to test this tool, with commands used to generate each stack trace:

CVE Enumeration	Command	Status	# Stack Traces
CVE-2020-7060	./php_fuzz inputs/crashes/id%3A000000	Control	51
CVE-2023-31722	./nasm_fuzz -f elf64 inputs/crashes/id%3A000000	Control	69
CVE-2021-20284	./nm-new_fuzz --synthetic inputs/crashes/id%3A000000	Variable	109
CVE-2022-44370	./nasm_fuzz -M inputs/crashes/id%3A000000	Variable	210
Each stack trace was saved in a .txt file. For instance:

zsh
Copy code
./php_fuzz inputs/crashes/id%3A000050 &> 50.txt
Disclaimer
As stated by OpenAI, ChatGPT may make mistakes. It's advisable to verify important information and consider second opinions. This tool is designed for educational purposes, and its effectiveness may vary.

Feel free to regenerate analysis results by typing readCrashes when prompted for further responses.










