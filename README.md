<img width="1888" height="1027" alt="image" src="https://github.com/user-attachments/assets/b2beba53-d493-42c9-8ea4-3c9cf1eb7bcf" />




# Research Assistant

Research Assistant is a Spring Boot backend with a companion Chrome extension for summarizing selected webpage text with Google's Gemini API. The extension opens as a browser side panel, sends selected text to the local backend, and displays the AI-generated summary alongside a small notes area.

## Project Structure

```text
research-assistant/
|-- research-assistant/       # Spring Boot API
|   |-- src/main/java/com/research/assistant/
|   |-- src/main/resources/application.properties
|   |-- pom.xml
|   `-- mvnw / mvnw.cmd
`-- research-assistant-ext/   # Chrome extension
    |-- manifest.json
    |-- sidepanel.html
    |-- sidepanel.js
    |-- sidepanel.css
    `-- background.js
```

## Features

- Summarize selected webpage text from a Chrome side panel.
- Send research requests to a local Spring Boot REST API.
- Generate responses with Gemini `2.5-flash`.
- Save simple research notes in Chrome local storage.
- Supports backend operations for `summarize` and `suggest`.

## Tech Stack

- Java 17
- Spring Boot 3.4.2
- Spring Web
- Spring WebFlux `WebClient`
- Lombok
- Maven
- Chrome Extension Manifest V3
- Google Gemini API

## Prerequisites

- Java 17 or newer
- A Gemini API key
- Google Chrome or another Chromium-based browser that supports Manifest V3 side panels

## Backend Setup

From the Spring Boot module:

```powershell
cd research-assistant
$env:GEMINI_KEY="your-gemini-api-key"
.\mvnw.cmd spring-boot:run
```

On macOS or Linux:

```bash
cd research-assistant
export GEMINI_KEY="your-gemini-api-key"
./mvnw spring-boot:run
```

The API starts on:

```text
http://localhost:8080
```

## API Usage

### Process Research Text

```http
POST /api/research/process
Content-Type: application/json
```

Request body:

```json
{
  "content": "Text to process",
  "operation": "summarize"
}
```

Supported operations:

- `summarize`: returns a concise summary.
- `suggest`: returns related topics and further reading suggestions.

Example with PowerShell:

```powershell
Invoke-RestMethod `
  -Uri "http://localhost:8080/api/research/process" `
  -Method Post `
  -ContentType "application/json" `
  -Body '{"content":"Spring Boot makes it easy to create stand-alone applications.","operation":"summarize"}'
```

## Chrome Extension Setup

1. Start the Spring Boot backend.
2. Open Chrome and go to `chrome://extensions`.
3. Enable `Developer mode`.
4. Click `Load unpacked`.
5. Select the `research-assistant-ext` folder.
6. Open any webpage, select text, and click the Research Assistant extension icon.
7. Click `Summarize` in the side panel.

The extension sends requests to:

```text
http://localhost:8080/api/research/process
```

## Configuration

Backend configuration is stored in:

```text
src/main/resources/application.properties
```

Current properties:

```properties
spring.application.name=research-assistant
gemini.api.url=https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash:generateContent?key=
gemini.api.key=${GEMINI_KEY}
```

Keep the API key in the `GEMINI_KEY` environment variable instead of committing it to source control.

## Build and Test

Build the backend:

```powershell
.\mvnw.cmd clean package
```

Run tests:

```powershell
.\mvnw.cmd test
```

## Notes

- The backend currently allows cross-origin requests from all origins with `@CrossOrigin(origins = "*")`.
- The Chrome extension currently exposes only the `summarize` operation in the UI.
- The backend `suggest` operation is available through the REST API but is not wired into the extension UI.
