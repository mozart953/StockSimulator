# StockSimulator

## Overview

StockSimulator is an inventory and sales simulation system for retail operations, designed to model point-of-sale behavior over defined time periods. The application generates simulated scenarios by executing stored procedures against a SQL Server database and presents analytical results through a web interface, including graphical charts and Crystal Reports.

The system operates under the database schema `DpanSimulacion` and is implemented as an ASP.NET MVC 5 web application targeting .NET Framework 4.8.

## Characteristics

- **Scenario simulation**: Executes a stored procedure (`CRT_ESCENARIO`) to simulate sales activity based on configurable parameters: number of hours, number of customers, and number of products.
- **Data modeling**: Uses Entity Framework 6 with multiple database contexts to access scenario details, customers, and time-based records.
- **Analytical reporting**: Provides multiple report models including top-selling products, least-selling products, customer-product breakdowns, and expense reports.
- **Report generation**: Integrates SAP Crystal Reports for formatted report output.
- **Web interface**: Razor view engine with Bootstrap for presentation, jQuery for client-side interaction, and Chart.js for graphical visualization.
- **Configurable database connections**: Supports multiple connection strings for different development environments.

## Architecture

### Application layers

- **Presentation**: ASP.NET MVC 5 controllers and Razor views (`Controllers/`, `Views/`).
- **Application startup**: `Global.asax` with `RouteConfig`, `FilterConfig`, and `BundleConfig` registered in `Application_Start`.
- **Domain models**: Entity Framework entity classes under `Models/` representing scenario, customer, and time details.
- **Data access**: Entity Framework `DbContext` classes (`Models/Model2.cs`, `Models/Model3.cs`) and direct SQL execution via `SqlConnection`/`SqlCommand` where required.
- **Reporting**: Report models under `Models/` (`ReporteClientesProductos`, `ReporteGasto`, `ReporteMasVenta`, `ReporteMenosVenta`, `ReporteTopProducto`) and Crystal Reports definitions under `DT_Reportes/`.

### Key components

| Component | Purpose |
| --- | --- |
| `HomeController` | Main controller handling scenario creation and navigation. |
| `DETALLE_ESCENARIO` | Entity/model for scenario records with methods `listar()` and `RealizarEscenario(string p_horas, string p_clientes, string p_productos)`. |
| `Model2` | Entity Framework context bound to connection `Model2`, exposing `DETALLE_ESCENARIO`. |
| `Model3` | Entity Framework context bound to connection `Model3`, exposing `DETALLE_CLIENTE` and `DETALLE_HORA`. |
| `Conexion` | Auxiliary class demonstrating direct SQL execution against the configured connection. |
| `CRT_ESCENARIO` (stored procedure) | Database-side procedure that generates simulation data based on input parameters. |

### Database

- **Default catalog**: `DpanSimulacion` (referenced by environment-specific connection strings).
- **Contexts**: `Model1`, `Model2`, `Model3` with `Model2`/`Model3` actively configured in `Web.config`.
- **Execution model**: Scenarios are created by calling `CRT_ESCENARIO` via `Database.ExecuteSqlCommand` or direct SQL.

## Technology stack

| Technology | Version |
| --- | --- |
| .NET Framework | 4.8 |
| ASP.NET MVC | 5.2.7 |
| Entity Framework | 6.1.3 |
| SQL Server | Compatible with Microsoft SQL Server (via `System.Data.SqlClient`) |
| Bootstrap | 5.1.3 |
| jQuery | 3.6.0 |
| Chart.js | 2.9.3 |
| Crystal Reports | 13.0.4001 |
| Newtonsoft.Json | 12.0.2 |

## Local deployment

### Prerequisites

- Microsoft Visual Studio 2019 or later with ASP.NET and web development workload
- .NET Framework 4.8 Developer Pack
- Microsoft SQL Server (local instance or accessible remote instance)
- SAP Crystal Reports runtime for Visual Studio (matching version 13.0.4001) if running reports locally
- Internet Information Services Express or IIS for local hosting

### Database setup

1. Create a database named `DpanSimulacion` on the target SQL Server instance.
2. Ensure the stored procedure `CRT_ESCENARIO` exists in the database. This procedure accepts parameters `@p_HORAS`, `@p_CLIENTES`, and `@p_PRODUCTOS` and populates the simulation tables (`DETALLE_ESCENARIO`, `DETALLE_CLIENTE`, `DETALLE_HORA`, and related entities).
3. Ensure required tables exist as mapped by Entity Framework models (`DETALLE_ESCENARIO`, `DETALLE_CLIENTE`, `DETALLE_HORA`).

### Application configuration

1. Open `Web.config` and update the connection strings to match the local environment:
   - `Model2`: used by `DETALLE_ESCENARIO` and scenario execution
   - `Model3`: used by customer and hour details
   - `conexion1`: auxiliary connection string
2. The file contains commented examples for different machines (`localhost` with SQL auth, `DESKTOP-M8SVI80\SQLEXPRESS`, `DESKTOP-E80R7VG\`). Select and configure the appropriate entry. By default, `Model2` and `Model3` are configured for `DESKTOP-E80R7VG\` with Windows authentication.
3. If using SQL authentication, update the credentials accordingly. If using integrated security, ensure the application pool identity has database access.

### Build and run

1. Clone or extract the repository to a local path.
2. Open `ProyectoSimulacion1.sln` in Visual Studio.
3. Restore NuGet packages: `Tools > NuGet Package Manager > Restore NuGet Packages`.
4. Build the solution: `Build > Build Solution`.
5. Run the application: `Debug > Start Debugging` (IIS Express) or deploy to a local IIS site.
6. Navigate to the application root. The default route maps to `Home/Index`.

### Usage

1. On the home page, provide values for hours, customers, and products.
2. Submit the form to execute the scenario via `CRT_ESCENARIO`.
3. On success, a confirmation message is displayed. Simulated results can be reviewed through the reporting views and Crystal Reports integrated in the application.

## Additional information

- **Connection strings**: Multiple environment configurations are present in `Web.config` and should be adjusted per deployment. Avoid committing production credentials.
- **Security considerations**: The current implementation constructs SQL for the stored procedure call in places using string concatenation. For production use, parameterized commands should be enforced.
- **Reports**: Crystal Reports dependencies are referenced via NuGet packages; proper runtime installation is required on the hosting server.
- **Client-side assets**: Bundles are registered in `BundleConfig` (not shown in full here) and loaded via `@Styles.Render`/`@Scripts.Render` in views.
- **Localization**: Spanish resource packages are included for MVC components.
- **Project type**: Web Application Project (`ProyectoSimulacion1.csproj`), not a SDK-style project.
