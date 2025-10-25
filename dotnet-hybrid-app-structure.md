# dotnet-hybrid-app-structure

## The Ideal Architecture

Your services—the core business logic, data access, and application state—should reside in a **standard .NET Class Library**.

Your UI components should be in a **Razor Class Library** (RCL).

This creates a clean, multi-layered architecture where your UI is completely decoupled from your business logic. The Blazor Web App and the .NET MAUI App both become thin "presentation layers" that consume the shared services.

### The Ideal Project Structure

Here’s what the solution structure and its dependencies would look like:

1. **`YourApp.Core` (Standard .NET Class Library)**
    * **Contents:**
        * Services (e.g., `ProductService`, `UserService`, `WeatherService`).
        * Data Models/Entities (e.g., `Product`, `User`).
        * Interfaces (e.g., `IProductService`).
        * Logic that uses `HttpClient` to call external APIs.
    * **Dependencies:** None. This library has zero dependencies on Blazor, MAUI, or any UI framework. It's pure C# logic.
    * **Java Analogy:** This is your primary "business logic JAR file."

2. **`YourApp.UI` (Razor Class Library - RCL)**
    * **Contents:**
        * All your `.razor` components (pages, layouts, shared components).
        * Shared static assets like CSS and JavaScript.
    * **Dependencies:** It **references `YourApp.Core`** so that your components can inject and use the services.
    * **Java Analogy:** This is your specialized "UI component JAR file."

3. **`YourApp.WebApp` (Blazor Web App Project)**
    * **Contents:**
        * The `Program.cs` file for web hosting.
        * Web-specific configurations (`appsettings.json`).
        * The `wwwroot` folder.
    * **Dependencies:** It **references `YourApp.UI`** (and transitively `YourApp.Core`).
    * **Responsibility:** To host the UI for the web.

4. **`YourApp.MauiApp` (.NET MAUI Blazor App Project)**
    * **Contents:**
        * The `Program.cs` file for MAUI app hosting.
        * Platform-specific resources (icons, splash screens).
        * The `BlazorWebView` control.
    * **Dependencies:** It also **references `YourApp.UI`** (and transitively `YourApp.Core`).
    * **Responsibility:** To host the UI for native desktop and mobile.

### Visualizing the Dependency Flow

```markdown
+---------------------------+       +---------------------------+
| YourApp.WebApp (Host)     |       | YourApp.MauiApp (Host)    |
+-------------+-------------+       +-------------+-------------+
              |                             |
              |      REFERENCES             |
              |                             |
              v                             v
+-------------------------------------------------------------+
| YourApp.UI (RCL - Razor Components, CSS)                    |
+-----------------------------+-------------------------------+
                              |
                              | REFERENCES
                              v
+-------------------------------------------------------------+
| YourApp.Core (Class Library - Services, Models, Logic)      |
+-------------------------------------------------------------+
```

### Why This is the Best Approach

* **Maximum Reusability:** Your core logic in `YourApp.Core` is now completely portable. You could add a third host, like a simple Web API or a Console App, and it could reuse the exact same services without any changes.
* **Separation of Concerns:** Each project has a single, clear responsibility. The UI doesn't know how data is fetched, and the services don't know how they are being displayed.
* **Testability:** This is a huge benefit. You can now write fast, simple unit tests for your services in `YourApp.Core` without needing to spin up a web host or a MAUI application.

### How to Register the Services

The beauty of this approach is that you register the exact same services in both hosts.

**In `YourApp.WebApp`'s `Program.cs`:**

```csharp
var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
builder.Services.AddRazorPages();
builder.Services.AddServerSideBlazor();

// Register your shared services from the .Core library
builder.Services.AddHttpClient(); // For services that need it
builder.Services.AddSingleton<IProductService, ProductService>();
builder.Services.AddSingleton<IUserService, UserService>();

var app = builder.Build();
// ...
```

**In `YourApp.MauiApp`'s `MauiProgram.cs`:**

```csharp
public static class MauiProgram
{
    public static MauiApp CreateMauiApp()
    {
        var builder = MauiApp.CreateBuilder();
        builder
            .UseMauiApp<App>()
            .ConfigureFonts(fonts =>
            {
                fonts.AddFont("OpenSans-Regular.ttf", "OpenSansRegular");
            });

        builder.Services.AddMauiBlazorWebView();
        #if DEBUG
      builder.Services.AddBlazorWebViewDeveloperTools();
      builder.Logging.AddDebug();
        #endif

        // Register the EXACT SAME shared services from the .Core library
        builder.Services.AddHttpClient();
        builder.Services.AddSingleton<IProductService, ProductService>();
        builder.Services.AddSingleton<IUserService, UserService>();

        return builder.Build();
    }
}
```

Your assessment was perfect. Separating the services into a standard class library is the key to building a clean, maintainable, and truly cross-platform Blazor application.

That's a crucial question that gets to the heart of managing a cross-platform project. The way you structure your repositories and plan your deployments is key to maintaining a smooth workflow.

Let's break down both aspects.

### Repository Structure: The Monorepo Advantage

No, they should almost always reside in a **single repository** as part of a **single .NET solution**. This is known as a monorepo approach, and it is the standard and highly recommended practice for this architecture.

Placing all four projects (`Core`, `UI`, `WebApp`, `MauiApp`) in one repository and one solution (`.sln`) file provides significant advantages:

1. **Simplified Dependency Management:** When you make a change in the `Core` library, all dependent projects (`UI`, `WebApp`, `MauiApp`) immediately see that change when you recompile the solution. There is no need to publish a package and then update dependencies.
2. **Atomic Commits:** A single feature might require changes in the `Core` services, the `UI` components, and the `WebApp` host. A monorepo allows you to commit all these related changes together in a single, atomic commit. This makes the code history much easier to understand.
3. **Seamless Refactoring:** IDEs like Visual Studio or JetBrains Rider can refactor code across all projects in the solution simultaneously. If you rename a method in your `Core` service, the IDE will automatically update its usage in your `UI` components. This is nearly impossible across different repositories.
4. **Unified Build Process:** You can build and test the entire application with a single command, ensuring all parts are compatible and working correctly.

Your file structure would look something like this:

```markdown
/YourAppSolution/
├── YourApp.sln
├── .gitignore
├── README.md
└── src/
    ├── YourApp.Core/
    │   └── YourApp.Core.csproj
    ├── YourApp.UI/
    │   └── YourApp.UI.csproj
    ├── YourApp.WebApp/
    │   └── YourApp.WebApp.csproj
    └── YourApp.MauiApp/
        └── YourApp.MauiApp.csproj
```

The multi-repo alternative would involve publishing your `Core` and `UI` libraries as NuGet packages. This adds significant overhead (versioning, package feed management, update coordination) and is generally only recommended for truly independent libraries shared across many unrelated applications and teams.

---

### Deployment: Different Paths for Different Hosts

While the code lives together, the deployment process is completely different for each "host" application. The libraries (`Core` and `UI`) are not deployed on their own; they are bundled into the hosts that reference them.

Here’s how each part gets deployed:

#### 1. `YourApp.Core` (Class Library) & `YourApp.UI` (RCL)

* **How they are deployed:** They aren't, directly. They are compiled into `.dll` files and are included as dependencies within the publish output of the `WebApp` and `MauiApp`.

#### 2. `YourApp.WebApp` (Blazor Web App)

* **What is the output?** A set of web files (HTML, CSS, JS, and DLLs).
* **What is the process?** You run the `dotnet publish` command on the `YourApp.WebApp` project.
* **Where does it go?** The output is deployed to a web hosting environment. This could be:
  * A cloud provider like Azure App Service.
  * An on-premise server running IIS.
  * A Linux server with a reverse proxy like Nginx.
  * A Docker container that is pushed to a container registry and run in an orchestrator like Kubernetes.
* **CI/CD Pipeline:** Your CI/CD pipeline (e.g., GitHub Actions, Azure DevOps) would be configured to build the solution, publish the `WebApp` project, and push the resulting artifacts to your chosen web host.

#### 3. `YourApp.MauiApp` (.NET MAUI App)

* **What is the output?** A native application package for each target platform.
* **What is the process?** You run the `dotnet publish` command on the `YourApp.MauiApp` project, often specifying the target platform. The build process is more complex and may require platform-specific build agents (e.g., you **must** use a macOS machine to build the iOS app) and code signing certificates.
* **Where does it go?** The packages are submitted to the respective app stores:
  * **Windows:** Microsoft Store (`.msix` package).
  * **Android:** Google Play Store (`.aab` package).
  * **iOS:** Apple App Store (`.ipa` package).
  * **macOS:** Mac App Store (`.pkg` package).
* **CI/CD Pipeline:** You would have a separate CI/CD pipeline (or a different stage in your main pipeline) specifically for building, signing, and publishing your MAUI app. This pipeline is often more complex due to the requirements of the various app stores.

### Summary Table

| Project | Repository | Deployable Unit? | Build Output | Deployment Destination |
| :--- | :--- | :--- | :--- | :--- |
| **`Core` & `UI`** | Monorepo | No (Library) | `.dll` files | Bundled inside the hosts |
| **`WebApp`** | Monorepo | **Yes (Host)** | Web files (HTML, CSS, JS, DLLs) | Web Server (Azure, IIS, etc.) or Container Registry |
| **`MauiApp`** | Monorepo | **Yes (Host)** | Native Packages (`.msix`, `.aab`, `.ipa`) | App Stores (Google Play, Apple App Store, etc.) |

In short: **One repo, one source of truth. Multiple, distinct deployment pipelines** tailored to the unique needs of each application host.
