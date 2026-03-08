<div align="center">

# i3xtools

### Tools for the i3X Ecosystem

**Turn any data source — databases, machine tools, files, devices — into an i3X-compliant API. Then visualize, aggregate, and query it with AI.**

[Website](https://i3x.net) · [Downloads](https://i3x.net/#/downloads)

</div>

> **What is i3X?** The [i3X standard](https://i3x.dev) is a REST API specification for contextualized manufacturing information, maintained by the i3X Working Group. These tools implement and extend that standard.

---

<div align="center">

| Tool | Description | Type | Download |
|------|-------------|------|----------|
| **[i3xdb](#-i3xdb--databases)** | Turn any relational database into an i3X API | Desktop + Server | [Releases](https://github.com/i3xtools/.releases/releases/tag/latest) |
| **[i3xmt](#-i3xmt--machine-tools)** | Bridge MTConnect devices and MQTT streams to i3X | Desktop + Server | [Releases](https://github.com/i3xtools/.releases/releases/tag/latest) |
| **[i3xcsv](#-i3xcsv--files)** | Convert CSV and Excel files into an i3X API | Desktop + Server | [Releases](https://github.com/i3xtools/.releases/releases/tag/latest) |
| **[i3xopc](#-i3xopc--opc-ua)** | Bridge OPC-UA address spaces to i3X context models | Desktop + Server | [Releases](https://github.com/i3xtools/.releases/releases/tag/latest) |
| **[i3xag](#-i3xag--aggregator)** | Merge multiple i3X servers into a single endpoint | Server | [Releases](https://github.com/i3xtools/.releases/releases/tag/latest) |
| **[i3xview](#-i3xview--graph-viewer)** | Interactive graph visualization of i3X object models | Desktop + Web | [Releases](https://github.com/i3xtools/.releases/releases/tag/latest) · [Web App](https://view.i3x.net) |
| **[i3xdash](#-i3xdash--dashboards)** | Build interactive dashboards from i3X data | Desktop + Web | [Releases](https://github.com/i3xtools/.releases/releases/tag/latest) · [Web App](https://dash.i3x.net) |
| **[i3xmcp](#-i3xmcp--mcp-server)** | Expose any i3X API as tools for AI agents | MCP Server | [Releases](https://github.com/i3xtools/.releases/releases/tag/latest) |
| **[i3xrag](#-i3xrag--rag--graph--mcp)** | Search, traverse, and analyze i3X object models with AI | MCP Server | [Releases](https://github.com/i3xtools/.releases/releases/tag/latest) |

</div>

---

## Data Source Adapters

### 🗄️ i3xdb — Databases

Desktop app with a visual mapping editor to configure which tables and columns become i3X objects. Supports PostgreSQL, MySQL, SQLite, Microsoft SQL Server, and Snowflake.

#### i3xdb-server

Headless server for production deployment. Requires a config file (exported from the desktop app) and a database connection string.

```bash
i3xdb-server --config <config.yaml> --connection <CONNECTION_STRING> [--port 8080]
```

**Database examples:**

```bash
# PostgreSQL
i3xdb-server -c config.yaml --connection "postgres://user:password@localhost:5432/mydb"

# MySQL
i3xdb-server -c config.yaml --connection "mysql://user:password@localhost:3306/mydb"

# Microsoft SQL Server
i3xdb-server -c config.yaml --connection "mssql://sa:Password@localhost:1433/mydb"

# SQLite
i3xdb-server -c config.yaml --connection "sqlite://./data.db"

# Snowflake
i3xdb-server -c config.yaml --connection "snowflake://user:password@account/database/schema?warehouse=WH&role=ROLE"
```

| Flag | Default | Description |
|------|---------|-------------|
| `-c, --config` | — | Path to YAML/JSON config exported from the desktop app |
| `--connection` | — | Database connection string |
| `-p, --port` | 8080 | HTTP port to listen on |

**Docker:**

```bash
# PostgreSQL
docker run -p 8080:8080 \
  -v ./config.yaml:/etc/i3xdb/config.yaml \
  i3xtools/i3xdb-server --connection "postgres://user:pass@host:5432/db"

# SQLite (mount the db folder)
docker run -p 8080:8080 \
  -v ./config.yaml:/etc/i3xdb/config.yaml \
  -v ./data:/etc/i3xdb/db \
  i3xtools/i3xdb-server --connection "sqlite:///etc/i3xdb/db/mydb.db"
```

---

### ⚙️ i3xmt — Machine Tools

Desktop app for connecting MTConnect agents and MQTT brokers, with visual device mapping and live value monitoring.

#### i3xmt-server

Headless server that subscribes to MTConnect device streams over MQTT and serves them as an i3X API.

```bash
i3xmt-server --broker <MQTT_URL> --device <DEVICE_UUID> [--config <config.yaml>] [--port 8080]
```

**Examples:**

```bash
# Basic — auto-discovers device model from MTConnect Probe
i3xmt-server --broker mqtt://192.168.1.100:1883 --device OKUMA.123456

# With exported config and custom port
i3xmt-server --broker mqtt://localhost:1883 --device Device:d1 --config device-config.yaml --port 3001

# Secure MQTT with authentication
i3xmt-server --broker mqtts://broker:8883 --device OKUMA.123456 --username admin --password secret --tls-insecure
```

| Flag | Default | Description |
|------|---------|-------------|
| `--broker` | — | MQTT broker URL (`mqtt://` or `mqtts://`) |
| `--device` | — | MTConnect device UUID |
| `--config` | — | YAML config with mapping + device model (optional — auto-discovers if omitted) |
| `-p, --port` | 8080 | HTTP port to listen on |
| `--username` | — | MQTT broker username |
| `--password` | — | MQTT broker password |
| `--tls-insecure` | false | Accept self-signed TLS certificates |

**Docker:**

```bash
docker run -p 8080:8080 \
  i3xtools/i3xmt-server --broker mqtt://192.168.1.100:1883 --device OKUMA.123456 --port 8080

# With config file
docker run -p 8080:8080 \
  -v ./device-config.yaml:/etc/i3xmt/config.yaml \
  i3xtools/i3xmt-server --broker mqtt://192.168.1.100:1883 --device OKUMA.123456 --config /etc/i3xmt/config.yaml --port 8080
```

---

### 📄 i3xcsv — Files

Desktop app for loading CSV and Excel files, mapping columns to i3X objects, and serving them through a built-in REST API.

#### i3xcsv-server

Headless server that reads CSV/Excel files and serves them as an i3X API.

```bash
i3xcsv-server --config <config.yaml> [--port 8080]
```

**Examples:**

```bash
i3xcsv-server --config config.yaml
i3xcsv-server -c config.json -p 3000
```

| Flag | Default | Description |
|------|---------|-------------|
| `-c, --config` | — | YAML/JSON config exported from the desktop app (includes file paths and mappings) |
| `-p, --port` | 8080 | HTTP port to listen on |

**Docker:**

```bash
docker run -p 8080:8080 \
  -v ./config.yaml:/etc/i3xcsv/config.yaml \
  -v ./data:/etc/i3xcsv/data \
  i3xtools/i3xcsv-server
```

---

### 🔌 i3xopc — OPC-UA

Desktop app for browsing OPC-UA address spaces and mapping nodes to i3X context models with live value subscriptions.

#### i3xopc-server

Headless server that subscribes to OPC-UA nodes and serves them as an i3X API.

---

### 🔀 i3xag — Aggregator

Merges multiple i3X servers into a single unified endpoint. Useful when data lives across several adapters and consumers need one API.

#### i3xag-server

```bash
i3xag-server --upstream <NAME=URL> [--upstream <NAME=URL> ...] [--port 9000]
```

**Examples:**

```bash
# Inline upstream servers
i3xag-server --upstream db=http://localhost:8080 --upstream mt=http://localhost:8081

# With config file
i3xag-server -c config.yaml

# Override port
i3xag-server -c config.yaml -p 3000
```

| Flag | Default | Description |
|------|---------|-------------|
| `--upstream` | — | Upstream server as `NAME=URL` (repeatable) |
| `-c, --config` | — | YAML config file with upstream list |
| `-p, --port` | 9000 | HTTP port to listen on |

Config file format:

```yaml
port: 9000
upstreams:
  - name: "SCADA"
    url: "http://localhost:8080"
  - name: "ERP"
    url: "http://localhost:8081"
```

#### Reparenting

When aggregating multiple servers, objects from different sources have independent hierarchies. A CNC machine discovered by i3xmt lives under MTConnect's root, while the equipment record in i3xdb lives under a database-defined hierarchy. They don't know about each other.

**Reparent rules** let you stitch these hierarchies together. You tell i3xag: "take this object from server A and make it a child of that object from server B." The aggregator rewrites `parentId` on the fly and synthesizes the corresponding `HasComponent`/`ComponentOf` relationships so the merged model looks like a single coherent tree.

```yaml
port: 9000
upstreams:
  - name: "database"
    url: "http://localhost:8080"    # i3xdb serving equipment records
  - name: "mtconnect"
    url: "http://localhost:8081"    # i3xmt serving live device data

reparent:
  # Nest the MTConnect device "Mazak" under the database equipment node
  - elementId: "Mazak"
    parentId: "Equipment:MX-001"

  # Nest another device under a different parent
  - elementId: "Okuma-LB3000"
    parentId: "Equipment:OK-002"
```

| Field | Description |
|-------|-------------|
| `elementId` | The object to re-parent (from any upstream server) |
| `parentId` | The new parent to assign (from any upstream server) |

This is how you build a unified plant model from separate data sources — databases provide the organizational structure, device adapters provide the live telemetry, and i3xag merges them into one API with a single hierarchy.

**Docker:**

```bash
# Inline upstreams
docker run -p 9000:9000 \
  i3xtools/i3xag-server --upstream db=http://host.docker.internal:8080 --upstream mt=http://host.docker.internal:8081 --port 9000

# With config file
docker run -p 9000:9000 \
  -v ./config.yaml:/etc/i3xag/config.yaml \
  i3xtools/i3xag-server -c /etc/i3xag/config.yaml
```

---

## Visualization

### 🔎 i3xview — Graph Viewer

Interactive graph visualization of i3X object models. Connects to any i3X server and renders the full object hierarchy — nodes, composition edges, relationships, and live sensor values — as a navigable canvas.

Available as a [web app](https://view.i3x.net) (no install) or a desktop app.

---

### 📊 i3xdash — Dashboards

Connect to any i3X server, browse the full object hierarchy, and build live dashboards with configurable tiles. Four tile types — value, gauge, sparkline, and state timeline — all updating in real time.

Available as a [web app](https://dash.i3x.net) (no install) or a desktop app.

**Features:**

- **Object browser** — navigate namespaces, types, and instances with filter/search
- **Live value panel** — select any object to see current value, quality, and timestamp
- **Four tile types** — value (single number), gauge (range arc), sparkline (trend line), timeline (discrete state bars)
- **Drag & resize** — move tiles to any grid position, resize by dragging edges or corners
- **Per-tile config** — units, decimals, thresholds, colors, Y-axis ranges, font size, and more
- **Save & load** — persist dashboard layouts to JSON (desktop: native file dialog, web: download/upload)

---

## AI Integration

### 🤖 i3xmcp — MCP Server

[MCP (Model Context Protocol)](https://modelcontextprotocol.io) server that exposes any i3X API as callable tools for AI agents. Runs over stdio — no CLI arguments needed.

**Tools exposed:**

```
connect           Connect to an i3X server URL
disconnect        Disconnect from the server
list_namespaces   List available namespaces
list_object_types List object types
list_objects      List object instances, filter by type
get_object        Fetch a single object by element ID
get_value         Read live value, quality, timestamp
get_values_bulk   Read values for multiple objects
get_related       Navigate relationships from an object
search_objects    Find objects by display name
```

**Claude Desktop configuration** (`claude_desktop_config.json`):

```json
{
  "mcpServers": {
    "i3xmcp": {
      "command": "C:/path/to/i3xmcp.exe",
      "env": {
        "RUST_LOG": "info"
      }
    }
  }
}
```

Once configured, ask Claude to `connect` to any i3X server URL and start querying.

---

### 🧠 i3xrag — RAG + Graph + MCP

Everything i3xmcp does, plus a BM25 search index and an in-memory graph database. On connect, crawls the entire i3X object model and builds both indexes. Agents can search with natural language, traverse relationships, find shortest paths, and aggregate data — all from a single crawl.

**Search tools:**

```
search             BM25 full-text search (query, limit, type_filter)
search_by_type     List or search objects within a type
describe_model     Summary of all types with object counts
search_related     Search + auto-traverse connected nodes in one call
```

**Graph tools:**

```
traverse           Walk N hops from a node, filter by edge/node type
find_path          Shortest path between two nodes
neighborhood       All nodes within N hops
subtree_summary    Count objects by type under a parent
ancestors          Walk up the hierarchy to root
aggregate          Group objects by type or parent
composition_tree   Nested parent-child hierarchy as a tree
relationship_map   Type-level relationship summary (SourceType → RelType → TargetType)
type_schema        Schema for a type: attributes, relationships, parent types
```

**Analysis tools:**

```
value_filter       Filter objects by value quality, missing values, or type
stale_values       Find objects with values older than a threshold
```

**Management tools:**

```
list_servers       Show configured server names (from I3XRAG_SERVERS env)
connect_and_index  Connect by name or URL, crawl/index the entire model
refresh_index      Re-crawl and rebuild both indexes
index_status       Show connection status, object/node/edge counts, index age
```

**Claude Desktop configuration** (`claude_desktop_config.json`):

```json
{
  "mcpServers": {
    "i3xrag": {
      "command": "C:/path/to/i3xrag.exe",
      "env": {
        "RUST_LOG": "i3xrag=info",
        "I3XRAG_LOG_DIR": "C:/logs",
        "I3XRAG_SERVERS": "ERP=http://localhost:8080,SCADA=http://localhost:8081"
      }
    }
  }
}

```

| Environment Variable | Default | Description |
|---------------------|---------|-------------|
| `RUST_LOG` | — | Log level (`info`, `debug`, `i3xrag=debug`) |
| `I3XRAG_LOG_DIR` | system temp | Directory for `i3xrag.log` file |
| `I3XRAG_SERVERS` | — | Named servers as `Name=URL` pairs, comma-separated |

Once configured, ask Claude to `list_servers` to see available servers, then `connect_and_index` by name (e.g. "ERP") to start exploring.

---

<div align="center">

The [i3X standard](https://i3x.dev) is maintained by the i3X Working Group.

</div>
