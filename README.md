# 31_MCP
Model Context Protocol MCP server

Testing a Model Context Protocol (MCP) server or client requires validating both low-level protocol mechanics and end-to-end LLM behavior. 
Unlike traditional APIs, MCP testing covers: 
transport handshakes
capability discovery
tool schemas
argument accuracy
model tool-selection quality

There is a consensus on Reddit's r/mcp that combining visual inspectors for manual debugging with automated unit tests, LLM evals, and ongoing regression fixtures gives the most reliable coverage as servers and models evolve.

Core Testing Layers
• Protocol & Transport Checks: Verify handshake behavior, stdio or HTTP/SSE transport connections, capability discovery, and OAuth/auth boundaries.
• Tool & Handler Unit Tests: Test individual tool execution logic, input validation, edge cases, and error responses directly.
• LLM & Multi-Turn Evals: Test whether models pick the right tool, pass valid arguments, handle multi-step flows, and avoid context overload when multiple servers are attached.
• Security & Regression Audits: Check for unauthenticated manifest access, over-broad permissions, prompt injection risks, and schema or response drift after updates.

Popular Tools & Workflows
• MCP Inspector (@modelcontextprotocol/inspector): Run npx @modelcontextprotocol/inspector locally to inspect capabilities, test tool calls interactively, and debug auth flows.
• Browser & Client Playgrounds: Use MCP Playground Online to test URL-reachable servers, mock servers, and client configs in the browser, or MCPJam for local testing, debugging, and evaluations across multiple AI clients.
• Automated Eval & CI Suites: Use frameworks like mcp-evals, DeepEval, or CLI runners in CI/CD to score tool selection, argument accuracy, and regression fixtures automatically.



MCP (SELENIUM / JAVA)
Using MCP with Java and Selenium typically means connecting an AI coding agent (such as Claude Desktop, Cursor, or VS Code Copilot) to a Selenium MCP server so the agent can drive a live browser, inspect the DOM, and generate or self-heal Java test suites (JUnit 5 or TestNG).

1. Core Setup

• Install Prerequisites: Ensure you have Java JDK 17+, Maven or Gradle, Node.js 18+ (to run the MCP server via npx), and a local browser (Chrome/Firefox).
• Configure the Selenium MCP Server: Add the community @angiejones/mcp-selenium server to your AI client's MCP configuration file (claude_desktop_config.json or .vscode/mcp.json):json
{
  "mcpServers": {
    "selenium": {
      "command": "npx",
      "args": ["-y", "@angiejones/mcp-selenium@latest"]
    }
  }
}
Use code with caution.
• Add Java Dependencies (pom.xml): Ensure your Java project includes selenium-java (4.x) and your preferred runner (junit-jupiter or testng). Selenium Manager automatically resolves browser drivers at runtime.

2. Key Workflows for Java QA

• Live DOM Discovery & POM Generation: Prompt your AI agent to open a URL via MCP, inspect the live accessibility tree or DOM elements, and generate a Java Page Object Model (@FindBy classes) paired with JUnit 5 or TestNG assertions.
• Self-Healing Broken Locators: When a Java test fails due to a UI change, have the agent launch the page via the MCP server, locate the updated CSS/XPath selector on the live page, and refactor your .java source file.
• Interactive Exploratory Testing: Give plain-English instructions (e.g., "Open Chrome, log in as a test user, complete checkout, and verify the confirmation message") to validate flows before writing permanent Java regression code.

3. Best Practices & Caveats

• Review Explicit Waits: AI-generated Selenium code often relies on basic locators; verify that WebDriverWait and ExpectedConditions are properly added to prevent flaky runs.
• Testing a Custom Java MCP Server: If you are instead building your own MCP server in Java (using the official MCP Java SDK or Spring AI) that wraps Selenium tools, test it with @modelcontextprotocol/inspector over stdio or HTTP/SSE alongside standard JUnit 5 integration tests.
Are you looking to:
• Use an existing Selenium MCP server to generate/maintain Java tests (with JUnit 5 or TestNG)?
• Build and test your own custom MCP server in Java that exposes Selenium WebDriver tools?
Let me know which path you're taking and I can share a complete starter template.



GitHub Copilot in IntelliJ IDEA functions as a full coding agent with:
local multi-file editing
terminal execution
cloud delegation
MCP support. Get started here:
⁠JetBrains Copilot Agent Documentation: Configure GitHub Copilot agent modes, permissions, and custom MCP servers in IntelliJ.

How It Works in IntelliJ
You can access Copilot's agentic features either through the GitHub Copilot JetBrains plugin or natively inside JetBrains AI Assistant / Air.
• Local Agent & Autopilot Modes: Unlike simple autocomplete or Q&A ("Ask" mode), Agent and Autopilot modes can autonomously plan tasks, edit multiple Java files, run Maven/Gradle terminal commands, read compiler or test errors, and iterate until fixes pass.
• MCP Server Support: Copilot in IntelliJ natively supports the Model Context Protocol (mcp.json or via Settings → Tools → AI Assistant → Model Context Protocol). This lets you attach a Selenium MCP server directly inside IntelliJ so Copilot can drive a browser and write or fix your Java tests.
• Cloud Coding Agent Delegation: From IntelliJ, you can also delegate asynchronous background tasks to GitHub's cloud coding agent (e.g., via @GitHub Copilot coding agent), which works independently in a cloud environment and opens a draft Pull Request on GitHub.

Quick Setup for MCP in IntelliJ
1. Install/Update: Ensure you have the latest GitHub Copilot plugin or an updated IntelliJ IDEA (2026.2+) with AI Assistant enabled.
2. Add Your MCP Server: Open the GitHub Copilot icon in the status bar → Edit Settings → Model Context Protocol → Configure (or configure under Settings → Tools → AI Assistant → Model Context Protocol) and add your mcp.json server entry.
3. Switch to Agent Mode: In the Copilot Chat panel, switch the dropdown from Ask to Agent (or Autopilot) and enable your MCP server tools in the tool picker.
Would you like the exact mcp.json configuration to connect a Selenium MCP server inside IntelliJ, or tips on configuring permissions so Copilot can run Maven/Gradle test commands automatically?




What does this do: Node.js 18+
Node.js 18+ provides the JavaScript runtime environment and npx command needed to download and run the Selenium MCP server locally on your machine. Even though your test code is in Java, most community MCP servers (including @angiejones/mcp-selenium) are built with TypeScript/JavaScript and require Node.js to execute.

Why You Need It
• Runs the MCP Server (npx): When IntelliJ starts the Selenium MCP server, it runs npx -y @angiejones/mcp-selenium@latest in the background. npx (bundled with Node.js) automatically downloads and runs the server without manual installation.
• Bridges IntelliJ and the Browser: The Node.js process acts as the translator—it receives tool calls from GitHub Copilot (like "click login button") and executes the actual browser commands via WebDriver.
• Modern Compatibility (18+): Version 18 or higher is required because modern MCP packages rely on built-in Node features (like native fetch and updated ES modules) introduced in Node 18+.

How to Check or Install
• Check if you already have it: Open IntelliJ's terminal and run node -v. If it outputs v18.x.x or higher (such as Node 20 or 22 LTS), you are ready to go.
• Install if missing: Download the LTS (Long Term Support) installer from nodejs.org or install via your package manager (brew install node on macOS, winget install OpenJS.NodeJS.LTS on Windows).



You can build a pure-Java Selenium MCP server using the official MCP Java SDK (io.modelcontextprotocol.sdk:mcp) and test it directly with JUnit 5—no Node.js required.
1. Maven Dependencies (pom.xml)
Add the official MCP Java SDK, Selenium Java, and JUnit 5 to your Maven project:

xml
<dependencies>
    <!-- Official MCP Java SDK (includes STDIO transport & Jackson) -->
    <dependency>
        <groupId>io.modelcontextprotocol.sdk</groupId>
        <artifactId>mcp</artifactId>
        <version>2.2.0</version>
    </dependency>

    <!-- Selenium WebDriver -->
    <dependency>
        <groupId>org.seleniumhq.selenium</groupId>
        <artifactId>selenium-java</artifactId>
        <version>4.27.0</version>
    </dependency>

    <!-- JUnit 5 -->
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter</artifactId>
        <version>5.11.4</version>
        <scope>test</scope>
    </dependency>
</dependencies>


2. Build the Java Selenium MCP Server (SeleniumMcpServer.java)

This server exposes Selenium browser actions (like open_url and get_title) over STDIO so GitHub Copilot in IntelliJ can launch and talk to it directly:
java
package com.example.mcp;

import io.modelcontextprotocol.server.McpServer;
import io.modelcontextprotocol.server.McpServerFeatures;
import io.modelcontextprotocol.server.McpSyncServer;
import io.modelcontextprotocol.server.transport.StdioServerTransportProvider;
import io.modelcontextprotocol.spec.McpSchema.*;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;

import java.util.List;
import java.util.Map;

public class SeleniumMcpServer {

    private static WebDriver driver;

    public static WebDriver getDriver() {
        if (driver == null) {
            ChromeOptions options = new ChromeOptions();
            options.addArguments("--headless=new"); // Remove if you want to watch the browser
            driver = new ChromeDriver(options);
        }
        return driver;
    }

    public static McpSyncServer createServer(StdioServerTransportProvider transport) {
        McpSyncServer server = McpServer.sync(transport)
                .serverInfo("java-selenium-mcp", "1.0.0")
                .capabilities(ServerCapabilities.builder().tools(true).build())
                .build();

        // Tool 1: Navigate to URL
        String openUrlSchema = """
                {
                  "type": "object",
                  "properties": {
                    "url": { "type": "string", "description": "Full URL to open" }
                  },
                  "required": ["url"]
                }
                """;

        server.addTool(new McpServerFeatures.SyncToolSpecification(
                new Tool("open_url", "Open a URL in Chrome via Selenium", openUrlSchema),
                (exchange, args) -> {
                    String url = (String) args.get("url");
                    getDriver().get(url);
                    return new CallToolResult(
                            List.of(new TextContent("Navigated to: " + getDriver().getCurrentUrl())),
                            false
                    );
                }
        ));

        // Tool 2: Get Page Title
        String emptySchema = """
                { "type": "object", "properties": {} }
                """;

        server.addTool(new McpServerFeatures.SyncToolSpecification(
                new Tool("get_title", "Get the current page title", emptySchema),
                (exchange, args) -> new CallToolResult(
                        List.of(new TextContent(getDriver().getTitle())),
                        false
                )
        ));

        return server;
    }

    public static void main(String[] args) {
        StdioServerTransportProvider transport = new StdioServerTransportProvider();
        createServer(transport);
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            if (driver != null) driver.quit();
        }));
    }
}
Use code with caution.


3. Test Your MCP Server with JUnit 5 (SeleniumMcpServerTest.java)

You can test your MCP server end-to-end in JUnit 5 by wiring an McpSyncClient to your server process over STDIO, listing the registered tools, and calling them just like an AI agent would:
java
package com.example.mcp;

import io.modelcontextprotocol.client.McpClient;
import io.modelcontextprotocol.client.McpSyncClient;
import io.modelcontextprotocol.client.transport.ServerParameters;
import io.modelcontextprotocol.client.transport.StdioClientTransport;
import io.modelcontextprotocol.spec.McpSchema.*;
import org.junit.jupiter.api.*;

import java.time.Duration;
import java.util.Map;

import static org.junit.jupiter.api.Assertions.*;

class SeleniumMcpServerTest {

    private static McpSyncClient client;

    @BeforeAll
    static void startMcpServer() {
        String javaBin = System.getProperty("java.home") + "/bin/java";
        String classpath = System.getProperty("java.class.path");

        ServerParameters params = ServerParameters.builder(javaBin)
                .args("-cp", classpath, "com.example.mcp.SeleniumMcpServer")
                .build();

        client = McpClient.sync(new StdioClientTransport(params))
                .requestTimeout(Duration.ofSeconds(30))
                .build();
        client.initialize();
    }

    @AfterAll
    static void stopMcpServer() {
        if (client != null) {
            client.closeGracefully();
        }
    }

    @Test
    void shouldListSeleniumTools() {
        ListToolsResult toolsResult = client.listTools();
        assertTrue(toolsResult.tools().stream().anyMatch(t -> t.name().equals("open_url")));
        assertTrue(toolsResult.tools().stream().anyMatch(t -> t.name().equals("get_title")));
    }

    @Test
    void shouldNavigateAndReadPageTitle() {
        CallToolResult navResult = client.callTool(
                new CallToolRequest("open_url", Map.of("url", "https://example.com"))
        );
        assertFalse(navResult.isError());

        CallToolResult titleResult = client.callTool(
                new CallToolRequest("get_title", Map.of())
        );
        TextContent content = (TextContent) titleResult.content().get(0);
        assertEquals("Example Domain", content.text());
    }
}
Use code with caution.

4. Connect Your Java MCP Server to GitHub Copilot in IntelliJ

1. Package your project into a runnable JAR (e.g., mvn clean package using maven-shade-plugin so all dependencies are bundled).
2. In IntelliJ, open Settings → Tools → AI Assistant → Model Context Protocol (or your Copilot mcp.json) and register your JAR:json
{
  "mcpServers": {
    "java-selenium": {
      "command": "java",
      "args": ["-jar", "/absolute/path/to/your-selenium-mcp-server.jar"]
    }
  }
}
Use code with caution.
3. Important Caveat: Never write debug System.out.println(...) statements in your MCP server code—STDIO transport uses standard output exclusively for JSON-RPC messages, so stray console prints will break the connection. Always log to System.err or an SLF4J file appender.

Would you like me to share:
• Additional Selenium tool handlers (such as click_element, type_text, and get_dom_snapshot)?
• The maven-shade-plugin snippet for pom.xml so you can build the runnable JAR for IntelliJ?
• Would you like to check which OS you're using so I can give you the exact command to install Node.js and verify npx works with IntelliJ?



