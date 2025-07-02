# Developing a Lightweight, Single-File Web-Based File Manager: A Technical Blueprint Inspired by Tiny File Manager

## I. Executive Summary

This report outlines a technical blueprint for developing a web-based file manager designed for simplicity, ease of use, and minimal dependencies. Drawing inspiration from the Tiny File Manager, the proposed solution will operate predominantly within a single PHP file, adhering to a strict "maximum of three files" constraint for the core application. Its fundamental functionalities will encompass basic file browsing, viewing file contents, and secure file uploading, all confined to a designated `/files` directory. The design prioritizes a clean, intuitive user interface, featuring both light and dark theme toggling, and multi-language support for English and Czech. A paramount focus is placed on integrating robust security measures to mitigate common web vulnerabilities, ensuring the file manager is both highly functional and secure for deployment.

## II. Introduction to the Minimalist File Manager Concept

### Defining the "Single-File, Lightweight" Paradigm for Web Applications

The concept of a "single-file, lightweight" web application represents a deliberate architectural choice, prioritizing ease of deployment and minimal operational overhead. Such applications are characterized by their self-contained nature, where the entire codebase, including server-side logic, client-side scripts, and presentation layers, resides within a minimal number of files, ideally just one. This approach offers distinct advantages, such as simplified deployment, often requiring nothing more than placing the single file on a PHP-enabled web server. This contrasts sharply with framework-heavy solutions that demand intricate setup processes, dependency installations, and significant resource consumption. For small utilities or personal tools where rapid setup and low maintenance are paramount, this paradigm is highly effective.

However, the commitment to a single-file structure, while simplifying external dependencies and deployment, inherently shifts the burden of modularity and dependency management from external frameworks or libraries to the internal organization of the code. This necessitates a highly disciplined coding approach within the single file to maintain readability, prevent the emergence of "spaghetti code," and ensure long-term maintainability. The developer must manually manage concerns that comprehensive frameworks typically handle, such as routing, templating, and the implementation of various security layers. The absence of an overarching framework means that the developer bears sole responsibility for structuring the monolithic file, ensuring that diverse concerns like HTML markup, CSS rules, JavaScript functions, and PHP logic are logically separated within the single file. This design choice, while delivering the desired deployment simplicity, introduces a potential for increased internal code complexity if not managed with extreme care. The "lightweight" aspect is achieved by omitting external framework overhead, but this requires the developer to be highly skilled in writing self-contained, well-structured, and manually secured code.

### Drawing Inspiration from Tiny File Manager's Architecture and Design Philosophy

The design of this minimalist file manager draws direct inspiration from the Tiny File Manager (TFM) project. TFM effectively demonstrates the viability of a functional, web-based file manager implemented primarily as a single PHP file.1 Its core design principles, particularly the embedding of user credentials (hashed passwords) directly within the main PHP file, align with the user's explicit requirement for "no configuration file" authentication.1 This approach minimizes external setup, making the application highly portable and easy to deploy.

In contrast, more complex, multi-file solutions like FileGator, while offering extensive features such as multiple storage adapters (FTP, Amazon S3, Dropbox), various authentication methods (JSON file, database, WordPress), and a single-page frontend built with modern JavaScript frameworks like Vue.js, deviate significantly from the "single-file" and "minimal dependencies" constraints.2 FileGator's repository structure, with distinct

`backend`, `frontend`, and `private` folders, along with numerous configuration and build files, clearly illustrates an architecture that prioritizes extensibility and advanced features over extreme minimalism.3 The proposed solution, by design, consciously avoids such complexities, reinforcing its deliberate choice for a highly minimalistic and straightforward implementation.

### Core Objectives

The primary objectives for this file manager are to achieve unparalleled simplicity, ensure ease of use for basic file operations, maintain minimal external dependencies, and provide a straightforward setup process. This focus ensures the utility is accessible and manageable for users who prioritize functionality over feature bloat.

## III. Core Architecture and Single-File Implementation (PHP)

### A. Single-File Structure: Strategies for Embedding HTML, CSS, and JavaScript

The entire application will be encapsulated primarily within a single `index.php` file. PHP's inherent capability to embed and process HTML, CSS, and JavaScript directly within its script allows for this monolithic structure.1 PHP code blocks, delineated by

`<?php...?>` tags, can be interspersed throughout the HTML, enabling dynamic content generation and server-side logic execution seamlessly within the same file.

The user's explicit constraint of "pouze max 3 soubory" (only max 3 files) provides a pragmatic interpretation of the "single-file" requirement. While theoretically all assets could be inlined within `index.php`, this constraint wisely allows for external CSS and JavaScript files to optimize performance and maintainability.

The recommended file structure to meet this constraint is:

1. `index.php`: This will serve as the main application file, containing all PHP logic, the primary HTML structure, and minimal, critical inline CSS and JavaScript necessary for initial rendering or very specific interactive elements.

2. `style.css`: A dedicated external CSS file will house the majority of the application's styling. This approach allows for browser caching of styles, which significantly improves load times on subsequent visits. It also provides a clear separation of presentation concerns from the core logic, making the code easier to manage and update.

3. `script.js`: A dedicated external JavaScript file will contain complex client-side interactivity, dynamic UI updates, and the logic for theme switching. Similar to the CSS file, this enables browser caching and promotes better code organization.

For truly tiny, frequently used assets, such as small icons or custom fonts, Base64 encoding can be employed to embed them directly into the CSS or HTML within `index.php`. This technique reduces the number of HTTP requests required for these micro-assets, potentially improving initial page load times.8 However, it is crucial to acknowledge that Base64 encoding increases the asset's size by approximately 33% and prevents browser caching of the individual asset.8 Therefore, this technique should be reserved for assets smaller than approximately 1KB to avoid unnecessarily bloating the main file and negatively impacting overall performance.

The following table provides a comparative overview of different strategies for embedding web assets within a single-file application context, illustrating the rationale behind the recommended "max 3 files" structure:

| Method                                 | Pros                                                                          | Cons                                                                                                              | Best Use Case (in this context)                                                     |
| -------------------------------------- | ----------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| **Inline HTML/CSS/JS**                 | Reduced HTTP requests; True single-file deployment                            | Increased HTML size; No individual asset caching; Maintainability challenges for large codebases                  | Critical initial styles; Very small, simple scripts; Essential HTML structure       |
| **External CSS/JS File**               | Browser caching; Separation of concerns; Easier maintainability; Cleaner HTML | Additional HTTP requests                                                                                          | Majority of application styling; Complex client-side logic; Theme switching scripts |
| **Base64 Data URI (for images/fonts)** | Reduced HTTP requests for micro-assets                                        | Increased file size (~33% bloat); No individual asset caching; Browser size limitations (e.g., IE8 limit of 32KB) | Very small, frequently used icons (e.g., < 1KB); Custom fonts                       |

### B. Minimal Dependencies: Leveraging PHP's Built-in Capabilities

The design of this file manager explicitly avoids the use of external PHP frameworks (such as Laravel or Symfony) and third-party libraries. All core functionalities, including file system interactions, authentication mechanisms, and user interface logic, will rely solely on PHP's robust set of built-in functions.4 This unwavering commitment to native functions is fundamental to achieving the lightweight nature and minimal dependency goals of the project.

While relying on built-in PHP functions is efficient and aligns with the "minimal dependencies" objective, it places a significant and critical responsibility on the developer to manually implement all necessary security validations and sanitization. Unlike comprehensive frameworks that often provide built-in protection mechanisms—such as Object-Relational Mappers (ORMs) for SQL injection prevention, auto-escaping templating engines for Cross-Site Scripting (XSS) defense, or integrated Cross-Site Request Forgery (CSRF) middleware—every interaction with user input or the file system in this single-file application must be explicitly secured by the developer. This means meticulous attention to detail in every function call that processes user data or interacts with files, ensuring that potential vulnerabilities are addressed at the lowest level of implementation. The benefit of a lightweight application comes with a higher security risk if the developer is not acutely aware of and diligent in applying manual security best practices for every single user interaction and file system operation.

## IV. Authentication and Security

### A. Password-Only Authentication: Implementing Secure Password Handling

The file manager will authenticate users based solely on a single, predefined password. This password will be checked against a stored hash. The password will not be stored in plaintext. Instead, PHP's `password_hash()` function, utilizing the `PASSWORD_DEFAULT` algorithm (which currently defaults to bcrypt), will generate a secure, salted, and hashed password. For verification, `password_verify()` will be used.1 This constitutes the industry-standard, cryptographically secure method for storing and verifying passwords.

To meet the "no configuration file" requirement, the hashed password will be hardcoded as a PHP variable (e.g., `$master_password_hash = 'GENERATED_HASH_HERE';`) directly within the `index.php` script. This approach mirrors how Tiny File Manager handles its `$auth_users` array.1

While `password_hash()` provides robust protection against direct decryption, hardcoding the hashed password directly into the `index.php` source code presents a significant security vulnerability if the file itself is ever exposed. Any attacker gaining read access to the `index.php` file—for instance, via a misconfigured web server serving PHP as plaintext, a directory traversal attack, or a source code leak—will immediately obtain the hashed password. This hash can then be subjected to offline brute-force or dictionary attacks, potentially compromising the system. The vulnerability does not lie in the strength of the hashing algorithm but in the storage location of the hash. Unlike a hash stored in a separate database, which would require a database breach in addition to code access, this hash is directly within the primary application file. Therefore, this design choice, while satisfying the explicit "no configuration file" constraint, trades off a layer of security. It is absolutely imperative that the web server configuration is meticulously secured to prevent PHP files from being served as plain text and to disable directory listings for the application's root directory.15 For production environments, storing credentials in environment variables or a dedicated, secure external secret management system would be fundamentally more secure, but this would contradict the user's "no configuration file" constraint.

### B. File System Operations Security

#### Designated Root Directory Enforcement (`/files`)

All file system operations, including browsing, viewing, and uploading, will be strictly confined to the predefined `/files` directory. This is a critical security measure to prevent unauthorized access to other parts of the server's file system. All user-provided paths will be rigorously validated using `realpath()` to convert them to absolute, canonical paths. Subsequently, these canonical paths will be checked with `strpos()` to ensure they consistently remain within the designated `/files` directory, effectively preventing path traversal attacks.16

#### Directory Browsing (`scandir()`)

The `scandir()` function will be utilized to list the contents of the current directory.19 When implementing directory browsing, several security measures are essential:

- Always filter out `.` (current directory) and `..` (parent directory) entries from the `scandir()` output to prevent trivial directory navigation exploits.19

- Combine `scandir()` with the `realpath()` and `strpos()` path validation mechanism to ensure that the requested directory is always a legitimate sub-directory of `/files`.16

- Implement additional logic to explicitly exclude sensitive files or directories (e.g., the `index.php` script itself, any temporary upload directories, or hidden files unless explicitly enabled by user preference) from the visible listing.1

It is important to note that while `scandir()` itself had a buffer overflow vulnerability (CVE-2012-2688), this specific flaw was patched in older PHP versions (5.4.5 and 5.3.15).21 For modern PHP installations, this particular vulnerability is not a concern. The primary security issue associated with

`scandir()` in contemporary applications is not an inherent bug in the function itself, but rather its misuse through unsanitized user input, which could lead to path traversal vulnerabilities. Therefore, the emphasis remains on robust input validation and path sanitization, rather than avoiding the function altogether.

#### File Content Viewing (`file_get_contents()`)

The `file_get_contents()` function will be used to read and display the contents of selected text files.22 For secure file content viewing, the following measures are imperative:

- Apply the same strict path validation using `realpath()` and `strpos()` as for directory browsing. This ensures that the requested file is located *only* within the designated `/files` directory and cannot be used to access arbitrary files outside this scope.16

- Crucially, all file content read using `file_get_contents()` *must* be escaped using `htmlspecialchars(..., ENT_QUOTES, 'UTF-8')` before being outputted to the HTML page. This prevents Cross-Site Scripting (XSS) attacks, where malicious scripts embedded within a text file (e.g., a `.txt` file containing `<script>alert('XSS');</script>`) could execute in the user's browser when the file's content is displayed.24

Arbitrary file read vulnerabilities are a severe risk. Even if the file manager only *displays* content, a weakness in path validation could allow an attacker to read sensitive system files (e.g., `/etc/passwd`, web server configuration files, or even the `index.php` source code itself to obtain the hashed password).23 The

`realpath()` and `strpos()` checks are therefore paramount not just for navigation but for *any* read operation, as compromising the ability to read arbitrary files can lead to significant information disclosure and subsequent system compromise.

#### File Uploads (`move_uploaded_file()`)

File uploads will be handled using PHP's `move_uploaded_file()` function, which ensures that only files genuinely uploaded via HTTP POST are processed.28 This function provides a baseline level of security by verifying the file's origin.

However, preventing arbitrary file uploads requires a comprehensive set of critical security measures:

- **File Type Validation (Whitelisting):** It is imperative to *never* rely solely on the client-provided file extension or the `$_FILES['type']` (MIME type) value, as these can be easily faked. Instead, perform server-side MIME type detection using functions like `finfo_file()` and/or `getimagesize()` (for image uploads) and compare the detected type against a strict whitelist of *allowed* file types/extensions (e.g., `jpg`, `png`, `pdf`, `txt`). Blacklisting known malicious extensions is insufficient as it can be bypassed through various techniques.28

- **File Size Limits:** Implement checks at both the PHP.ini level (`upload_max_filesize`, `post_max_size`) and within the application code (by checking `$_FILES['size']`) to prevent Denial-of-Service (DoS) attacks from excessively large uploads that could exhaust server resources.28

- **File Name Sanitization and Renaming:** Always use `basename()` on the uploaded filename to strip any directory components, which helps prevent path traversal attacks.28 More importantly,
  
  *always* rename uploaded files to a unique, non-guessable, and non-executable name. This can be achieved by using a combination of `uniqid()`, `microtime()`, or a hash of the file content like `sha1_file()`, combined with a whitelisted extension. Renaming prevents overwriting existing files and, crucially, prevents attackers from uploading malicious scripts (e.g., PHP web shells) and then executing them by their original name.28

- **Secure Storage Location:** While the user query specifies the `/files` directory, the strongest security practice is to store uploaded files *outside* the web root (i.e., in a directory not directly accessible via a URL) and serve them via a proxy script.28 If the
  
  `/files` directory *must* reside within the web root, it is paramount to configure the web server (Apache/Nginx) to explicitly prevent direct execution of any script files (e.g., `.php`, `.py`, `.exe`, `.sh`) within that directory.

Arbitrary file upload is consistently ranked as one of the most critical web vulnerabilities.34 If an attacker can upload a malicious executable file (e.g., a PHP web shell) and subsequently trigger its execution, they can gain full control over the server. The comprehensive set of validation and sanitization steps outlined above (file type, size, renaming, and secure storage location) is not merely a best practice; it is an

*absolute imperative* to prevent a complete system compromise. The lightweight nature of this application means the developer has no framework-level safeguards; every validation and sanitization step must be manually and correctly implemented. Failure to do so transforms the simple file manager into a critical security hole that could lead to full server compromise.

### C. General Web Security Measures

#### Cross-Site Scripting (XSS) Prevention

All user-generated content or dynamically displayed file system data (e.g., file names, directory names, and particularly file contents when viewed) *must* be escaped before being rendered in HTML. The `htmlspecialchars()` function, with the `ENT_QUOTES` flag and `UTF-8` encoding, is the primary defense against XSS attacks in PHP.24 This prevents malicious scripts, potentially injected into filenames or file contents, from executing in the user's browser, thereby protecting the integrity of the user's session and the application interface.

#### Cross-Site Request Forgery (CSRF) Prevention

A basic CSRF token mechanism should be implemented for all state-changing HTTP POST requests, such as file uploads, and any potential future functionalities like file deletions or renames. A unique, cryptographically secure token should be generated for each user session using `bin2hex(random_bytes(32))`, stored in the `$_SESSION` superglobal, embedded as a hidden input field in the relevant forms, and then verified upon form submission.26

Even a seemingly simple, single-file application is vulnerable to CSRF if it performs sensitive actions (like file uploads or deletions) via POST requests without proper token validation. An attacker could craft a malicious webpage that, when visited by an authenticated user, silently triggers unintended actions on the file manager without the user's knowledge. This occurs because the browser automatically sends the user's session cookies with the request. Manually implementing CSRF tokens is a fundamental security requirement, not just for complex applications, to ensure that requests originate from the legitimate application interface and not from a malicious third party.

## V. User Interface and Experience

### A. Minimalistic Design

The user interface will adhere to a clean, uncluttered aesthetic, prioritizing core functionality and ease of navigation. The design will employ clear typography and a consistent layout, ensuring that users can quickly grasp how to browse, view, and upload files without needing extensive instructions. A simple layout with a clear path display (breadcrumbs), a file listing area, and dedicated sections for actions (e.g., an upload button) will contribute to an intuitive user experience. Basic responsive CSS techniques will be employed to ensure usability across various screen sizes, from desktop monitors to tablets and mobile devices.

### B. Theme Switching (Light/Dark Mode)

Light and dark themes will be managed primarily through CSS. This can be achieved by defining CSS variables for colors and backgrounds that are swapped based on the active theme, or by applying distinct CSS classes (e.g., `.light-mode`, `.dark-mode`) to the `<body>` element of the HTML document.36 A simple JavaScript function will be responsible for toggling these CSS classes or updating the CSS variables based on user interaction, such as a button click. To ensure the user's theme preference persists across sessions, the chosen theme will be stored client-side using

`localStorage` or server-side in a PHP session variable (`$_SESSION['theme']`).

### C. Multi-Language Support (EN/CZ)

All user-facing text strings will be stored in PHP associative arrays directly within the `index.php` file. Separate arrays will be defined for each language (e.g., `$lang_en`, `$lang_cz`), or a single array with nested language keys. A global `$lang` variable will then be dynamically set to point to the active language array based on user selection. A simple UI element, such as a dropdown menu or two flag icons, will allow users to select their preferred language. This selection will update a PHP session variable (`$_SESSION['lang']`) to persist the choice across subsequent requests.38 All dynamic text output within the application will then reference the

`$lang` array (e.g., `echo $lang['upload_button_text'];`).

The following table illustrates an example of how the language array structure would be implemented within the single PHP file:

| Key                       | English Value               | Czech Value                  |
| ------------------------- | --------------------------- | ---------------------------- |
| `login_title`             | File Manager Login          | Přihlášení Správce Souborů   |
| `username_label`          | Username                    | Uživatelské jméno            |
| `password_label`          | Password                    | Heslo                        |
| `login_button`            | Log In                      | Přihlásit se                 |
| `browse_files_button`     | Browse Files                | Procházet soubory            |
| `upload_file_button`      | Upload File                 | Nahrát soubor                |
| `current_directory_label` | Current Directory:          | Aktuální adresář:            |
| `file_name_column`        | File Name                   | Název souboru                |
| `file_size_column`        | Size                        | Velikost                     |
| `view_content_button`     | View Content                | Zobrazit obsah               |
| `theme_switch_label`      | Toggle Theme                | Přepnout téma                |
| `file_uploaded_success`   | File uploaded successfully. | Soubor byl úspěšně nahrán.   |
| `file_upload_error`       | Error uploading file.       | Chyba při nahrávání souboru. |
| `invalid_password`        | Invalid password.           | Neplatné heslo.              |

## VI. Deployment and Management

### Simplified Deployment Process

The primary advantage of a single-file PHP application lies in its straightforward deployment process. Users are only required to place the `index.php` file (and optionally the `style.css` and `script.js` files if they are kept external) into a web server's document root (e.g., Apache, Nginx). The web server must have PHP installed and configured to correctly process `.php` files.6 This eliminates the need for complex database setups, framework installations, or dependency management tools like Composer or npm, aligning perfectly with the "minimal dependencies" goal and ensuring a quick and hassle-free setup.

### Managing the Designated `/files` Directory

The `/files` directory, where all user-managed files will reside, should be created in the same location as `index.php` or a clearly defined relative path that is accessible by the web server.

A crucial aspect of managing this directory involves meticulous web server configuration. It is paramount to configure the web server to prevent direct execution of any script files (e.g., `.php`, `.py`, `.exe`, `.sh`) within the `/files` directory. This configuration mitigates the significant risk of an attacker uploading a malicious script and subsequently triggering its execution directly via a web request.28 Furthermore, directory listing for the

`/files` folder should be explicitly disabled in the web server configuration to prevent unauthorized information disclosure, which could reveal the structure and contents of the directory to attackers.15 Regular backups of the

`/files` directory are strongly recommended, as it contains all user-managed data, and data loss could be catastrophic.

## VII. Recommendations and Future Considerations

### Summary of Critical Security Best Practices

Developing a lightweight, single-file application necessitates a heightened awareness and diligent implementation of security measures, as the integrated security layers often found in larger frameworks are absent. The following practices are critical for maintaining the integrity and confidentiality of the file manager:

- **Web Server Configuration is Paramount:** The web server must *never* be configured to serve PHP files as plain text. It is essential to ensure that the server correctly processes all `.php` files. Explicitly disable directory listing for both the application's root directory and the `/files` directory to prevent information leakage that could aid an attacker.15

- **Keep PHP Updated:** Regularly update the PHP installation to the latest stable version. This is crucial for patching known vulnerabilities, ensuring optimal security, and benefiting from performance improvements.21

- **Rigorous Input Validation:** Sanitize *all* user input, especially file paths. Utilize functions like `basename()` to strip directory components and `realpath()` to resolve paths to their canonical, absolute form. These should be combined with checks (e.g., `strpos()`) to ensure all file operations remain strictly within the designated `/files` directory, thereby preventing path traversal attacks.16

- **Comprehensive File Upload Security:** This component represents the most significant attack surface. Implement strict whitelisting for allowed file types using server-side MIME type detection (`finfo_file()`, `getimagesize()`). Enforce file size limits at both the PHP.ini level and within the application code. Most importantly, *always* rename uploaded files to unique, non-guessable, and non-executable names to prevent arbitrary file execution.28 If feasible, storing uploads outside the web root is the most secure approach; otherwise, strict web server rules preventing execution within the
  
  `/files` directory are essential.

- **Secure Password Hashing:** Always use `password_hash()` for storing passwords and `password_verify()` for verification. While this provides robust cryptographic security, it is important to understand that hardcoding the password hash directly in the source file, though meeting the "no configuration file" requirement, introduces a risk if the source code itself is compromised.13

- **XSS Prevention:** Escape *all* user-generated or dynamic content displayed in HTML using `htmlspecialchars()` (with `ENT_QUOTES` and `UTF-8` encoding) to prevent Cross-Site Scripting attacks.24

- **CSRF Protection:** Implement CSRF tokens for all forms that trigger state-changing actions (e.g., file uploads). This involves generating a unique, cryptographically secure token per session, embedding it in forms, and verifying it upon submission to prevent Cross-Site Request Forgery attacks.26

- **Disable Error Reporting:** In a production environment, disable detailed PHP error reporting (`display_errors = Off` in `php.ini`) to prevent sensitive system information from being leaked to potential attackers.16

### Potential Enhancements for Extended Functionality

While the current blueprint focuses on core requirements, the architecture allows for future expansion:

- **Basic File Editing:** Extend the file content viewing functionality to allow authenticated users to edit and save changes to text-based files directly through the web interface.

- **Download Options:** Implement functionality for downloading individual files and potentially zipping and downloading entire directories.

- **Multi-User Support:** While the current scope is single-password, the architecture could be extended to support multiple users with different roles and directory access, similar to Tiny File Manager's `$auth_users` array.1 This would require careful management of user credentials and permissions.

- **File Deletion and Renaming:** Add capabilities for deleting and renaming files and folders, ensuring all operations are subject to the same strict path validation and authentication checks.

### Limitations and Scalability Considerations of the Single-File Approach

Despite its advantages in simplicity and deployment, the single-file approach has inherent limitations:

- **Maintainability:** While simple for initial basic features, the single-file approach can become increasingly challenging to maintain and debug as features grow due to the lack of clear separation of concerns inherent in a monolithic file.

- **Performance for Large Directories:** Listing very large directories with thousands of files using `scandir()` and processing them entirely in memory might impact performance and resource consumption.

- **Security Burden:** As highlighted, security relies entirely on diligent manual implementation by the developer. This approach lacks the integrated, often battle-tested security layers typically provided by modern web frameworks, placing a higher responsibility on the implementer.

- **Scalability:** This solution is best suited for personal use or very small teams and is not designed for high-traffic environments, large-scale concurrent users, or enterprise-level file management needs. Its minimalist design prioritizes ease of use and deployment over robust scalability features.
