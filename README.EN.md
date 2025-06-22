# AI Agent Long Paper Writing Tutorial

> [简体中文](README.md) | English

This tutorial describes how to use the [Roo Code Agent](https://roocode.com) extension for [Visual Studio Code](https://code.visualstudio.com/Download) to write long academic papers.

## Tutorial

### Install Dependencies

1. Install [Visual Studio Code](https://code.visualstudio.com/Download);
2. Install [Git](https://git-scm.com/downloads).

### Agent Configuration

1. Launch [Visual Studio Code](https://code.visualstudio.com/Download);
2. Install project environment-related extensions (optional. Recommended if you have reference projects);
3. Install the [Roo Code](https://roocode.com) extension within VS Code;
4. Click the left-side kangaroo (Roo Code) icon to configure the LLM provider API and model (prioritize models with longer context windows; `as of June 22, 2025`, DeepSeek-V3-0324 is recommended as many platforms offer free API access);
5. In Roo Code's `Context` settings, appropriately increase the `Concurrent File Read Limit` and set `File Read Auto-Truncate Threshold` to `Always Read Entire File`.

### Project Setup

1. Launch a terminal in your usual projects folder (the parent folder of project directories); or use `cd` command to navigate to it;
2. Run `git clone https://github.com/PJ-568/long-paper-ai-agent-tutorial.git --depth=1`. If encountering network issues, try `git clone https://github.com/PJ-568/long-paper-ai-agent-tutorial.git --depth=1`. This will create a folder named `long-paper-ai-agent-tutorial`;
3. Run `cd long-paper-ai-agent-tutorial` to enter the project directory;
4. Run `code .` to launch VS Code with this folder as workspace.

> Due to security considerations for `githubfast.com`, please use this domain only in non-production environments.

### File Path Optimization (Optional)

1. Copy or symlink files/folders requiring agent access into this project folder.

> Note: If using symlinks, please inform the agent in prompts ("Certain files/folders are symlinks, hence not listed in the system...").

### Document Format Optimization

To optimize the report template's style and format:

1. Modify the prompt below, replacing `<file_path/filename>` with relative paths to requirement documents (obtain via right-click → `Copy Relative Path` in VS Code file explorer);

   For multiple files, use format like `` `<file_path1/filename1>`, `<file_path2/filename2>`, `<file_path3/filename3>` ``.

   ```markdown
   # Document Format Optimization
   
   Your task is to optimize the report template's style and format based on obtained requirements.
   
   ## **Requirements**
   
   **Strictly follow these rules:**
   
   - **Only modify the template when its style/format conflicts with requirements;**
   - **Do NOT delete or alter examples/comments in the template;**
   - **Only adjust non-compliant style elements, unless heading formats severely violate requirements.**
   
   ## Tasks
   
   1. Read `<file_path/filename>` to understand report requirements.
   2. If requirements include document formatting rules, modify `report_template.html` accordingly.
   ```

2. Create a new Roo Code session in `Code` mode, paste the modified prompt and send;
3. Wait for agent completion;
4. Open `report_template.html` in a browser to verify compliance;
5. If requirements aren't met, request agent modifications until satisfied.

> Monitor the context length progress bar during agent operation; complete tasks before exceeding maximum context.

### Document Writing

1. Modify the prompt below:
   - Replace `<!-- Project description and state; files requiring agent review -->` with necessary project information;
   - Specify files for review using relative paths (via right-click → `Copy Relative Path`), e.g., `<file_path/filename>`;
   - For multiple files, use `` `<file_path1/filename1>`, `<file_path2/filename2>`, `<file_path3/filename3>` ``;
   - Replace `【Section Number】Section Title` with actual section numbering and title.

   ```markdown
   # Document Writing
   
   Your task is to write report fragments.
   
   ## **Requirements**
   
   **Strictly follow these rules:**
   
   - **Each response contains only one HTML fragment:**
     - Pure HTML, *no* CSS;
     - Direct content HTML, *without* section headings or wrappers like `<div class="content"></div>`.
   - **Use section numbers as filenames:**
     - E.g., for content between `<h2>3 System Requirements Analysis</h2>` and `<h3>3.1 Business Process Analysis</h3>`, write to `.\parts\3.html` (filenames use section numbers: `3.3.html`, `3.3.1.html`, etc.);
     - Handle numbering discrepancies in the template.
   - **Represent relationships, architectures, states, dataflows, models, processes, etc., using PlantUML code blocks;**
   - **For non-PlantUML diagrams (e.g., screenshots), follow template examples and write `[Placeholder for ... diagram]` for manual insertion later;**
   - **Content should be detailed and substantive—more words preferred;**
   - ***Do NOT* include citations.**
   
   ## Tasks
   
   1. <!-- Project description and state; files requiring agent review -->
   2. Read `report_template.html`.
   3. **Complete the `【Section Number】Section Title` section.**
   ```

2. Create new Roo Code session in `Code` mode, paste modified prompt and send;
3. Wait for first task completion;
4. Verify `report_template.html` in browser;
5. If unsatisfied, request modifications;
6. If satisfied, send next task: ``Good. Now: **Complete the `【Section Number】Section Title` section.**`` (replace with actual section);
7. Wait for completion;
8. Click the trash can icon next to the task and select `Delete this message and all following messages` (prevents context overflow);
9. Repeat steps 6-8 for all sections.

> Monitor context length progress bar during agent operation.

### Polishing Results

These steps require manual operation (currently difficult to automate):

1. Copy `report_template.html` to `report.html`;
2. Manually insert contents from `parts` folder into corresponding positions in `report.html`;
3. Create new Roo Code session in `Ask` mode, request agent review of `report.html` for redundancies/errors;
4. Manually edit redundancies; for logical errors, follow steps 6-9 in [Document Writing](#document-writing) to revise;
5. Find all `@startuml` in `report_template.html`, render each via [PlantUML Web Server](https://www.plantuml.com/plantuml/uml), convert images to [data URLs](https://lmstfy.icu/Bing/?q=convert+image+to+data+url) and replace:

   ```diff
   -   <div class="code-block">
   -     <pre>@startuml
   - Bob -> Alice : hello
   - @enduml</pre>
   +   <div class="diagram">
   +     <img src="data:image/png;base64,..." alt="Greeting Flowchart"></img>
   +     <p>Fig.1 Greeting Process</p>
       </div>
   ```

6. Find all `【` in `report_template.html`, convert required screenshots to [data URLs](https://lmstfy.icu/Bing/?q=convert+image+to+data+url) and replace:

   ```diff
       <div class="diagram">
   -     <img src="" alt="[Placeholder for frontend screenshot]"></img>
   -     <p>Fig.X [Placeholder for frontend screenshot]</p>
   +     <img src="data:image/png;base64,..." alt="Frontend Screenshot"></img>
   +     <p>Fig.2 Frontend</p>
       </div>
   ```

### Format Conversion

HTML to XeTeX/PDF conversion omitted. Example for HTML to DOCX:

1. Open [HTML to DOC - Online Converter](https://www.aconvert.com/cn/document/html-to-doc);
2. Upload HTML file;
3. Select DOCX format;
4. Convert and download. If successful, open in DOCX editor (e.g., Microsoft Word) and proceed to step 6;
5. If conversion fails after 5 attempts, open HTML directly in DOCX editor;
6. Copy content from converted file and paste with "Merge Formatting" into the institutional template, then save as DOCX;
7. Adjust document styling, image sizing and positioning.

## Examples

- [Software Engineering Practice Course Design Report](无需智能体阅读的参考/《软件工程实训》课程设计实验报告书.html)
- [Dual-end Engineering Report](无需智能体阅读的参考/双端工程实验报告.html)

## Code or Content Contributions

We welcome contributions of code and content from anyone interested in this project.

For a simpler way to contribute, please refer to the [Contribution Guide](CONTRIBUTING.md) for relevant information.

## License

This project follows [CC BY-SA 4.0 International](https://creativecommons.org/licenses/by-sa/4.0/). For full license text, see [`LICENSE`](LICENSE).
