---
name: Update Resume
description: Automates the process of adding new job experiences to resume.md, handling HTML table formatting and class alternation.
---

# Update Resume Skill

This skill allows the AI agent to accurately and efficiently update `resume.md` with new job experiences. The resume uses raw HTML tables for layout, which requires careful handling of tags and CSS classes.

## Prerequisites

Before applying changes, ensure you have the following information from the user:
1.  **Company Name**: The company where the work was performed.
2.  **Period**: Start and End date (e.g., "2025年6月 〜 現在").
3.  **Project Title**: Summary of the project (e.g., "新規Webサービス開発").
4.  **Details**: Bullet points of tasks/achievements.
5.  **Phase**: e.g., "要件定義", "設計", "実装".
6.  **Tech Stack**: Languages, Tools, OS (e.g., "TypeScript", "React", "AWS").
7.  **Role/Team Size**: e.g., "テックリード / 5名".

## Procedure

### 1. Identify the Target Table

-   Search `resume.md` for the **Company Name**.
-   **If found**: You will append a new row to this company's `<table>`.
-   **If NOT found**: You need to create a new `<table>` block at the top of the "職務経歴" section (below `### ■ 職務経歴`).

### 2. Construct the HTML Row

Use the following template. **IMPORTANT**: Determine the correct class (`odd` or `even`) based on the insertion context.

```html
    <tr class="{CLASS}">
      <td>
        <p>{START_DATE}</p>
        <p>〜</p>
        <p>{END_DATE}</p>
      </td>
      <td>
        <p>{PROJECT_TITLE}</p>
        <p>&lt;概要&gt;</p>
        <ul>
          <li><p>{DETAIL_1}</p></li>
          <li><p>{DETAIL_2}</p></li>
        </ul>
      </td>
      <td>
        <p>{PHASES}</p>
      </td>
      <td>
        <p>{TECH_STACK}</p>
      </td>
      <td>
        <p>{ROLE}</p>
        <p>{TEAM_SIZE}</p>
      </td>
    </tr>
```

*Note: Format text with one entry per `<p>` tag where appropriate, matching existing style.*

### 3. Insertion Logic

#### Scenario A: Adding to Existing Company

1.  Locate the header rows of the company table:
    ```html
    <tr class="odd"><td colspan="5">...Company Info...</td></tr>
    <tr class="even"><td><p>期間</p></td>...Header...</tr>
    ```
2.  The new row should be inserted **immediately after** the header row (the `tr` containing "期間").
3.  **Class Determination**:
    *   The header row is usually `even`.
    *   Therefore, your NEW row should be `class="odd"`.
4.  **Rebalancing Classes**:
    *   Check the row that was previously at the top. If it was `odd`, it is now pushed down to an `even` position.
    *   You MUST update the class of the row immediately following your insertion from `odd` -> `even` (and potentially the next one `even` -> `odd`) to maintain the visual striping.
    *   If maintaining the entire table's striping is too large a diff (e.g. > 20 rows), at least ensure the top 3 rows alternate correctly so functionality isn't broken.

#### Scenario B: New Company

1.  Create a new `<table>` block.
2.  Structure:
    ```html
    <table>
      <tbody>
        <tr class="odd">
          <td colspan="5">
            <p>□{START_DATE}〜{END_DATE}　{COMPANY_NAME}</p>
            <p>◆事業内容：{BUSINESS_TYPE}</p>
            <p>◆従業員数：{EMPLOYEE_COUNT}</p>
          </td>
        </tr>
        <tr class="even">
          <td><p>期間</p></td>
          <td><p>プロジェクト内容</p></td>
          <td><p>担当フェーズ</p></td>
          <td><p>環境/規模</p></td>
          <td><p>メンバー/役割</p></td>
        </tr>
        <!-- Insert your new job row here (class="odd") -->
      </tbody>
    </table>
    ```
3.  Insert this entire block immediately after `### ■ 職務経歴`.

## 4. Execution

-   Use `replace_file_content` to insert the text.
-   Run `view_file` afterwards to verify the table syntax is correct (tags closed properly) and indentation looks consistent.
