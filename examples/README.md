# Meegle CLI [translated text] V1 [translated text] [translated text]

V1 [translated text] “ [translated text] [translated text] + [translated text] [translated text] [translated text] ”： [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] `main` [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] `meegle`。 [translated text] [translated text] [translated text] Meegle [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ；V1 [translated text] [translated text] [translated text] [translated text] [translated text] 。

## 1. [translated text] [translated text] [translated text]

 [translated text] [translated text] [translated text] [`no-extension/main.go`](./no-extension/main.go)：

```go
package main

import (
	"os"

	meeglecmd "github.com/larksuite/meegle-cli/cmd"
)

func main() {
	os.Exit(meeglecmd.Execute())
}
```

 [translated text] [translated text] CLI [translated text] [translated text] [translated text] ：

```bash
go build -o meegle ./examples/no-extension
./meegle --help
./meegle auth login --device-code
./meegle workitem get --project-key demo --work-item-id 123
```

 [translated text] [translated text] [translated text] [translated text] ， [translated text] 、 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。

## 2. [translated text] [translated text] CLI [translated text] [translated text] [translated text]

 [translated text] [translated text] [translated text] [`enterprise-cli/main.go`](./enterprise-cli/main.go)：

```go
package main

import (
	"os"

	meeglecmd "github.com/larksuite/meegle-cli/cmd"
	_ "github.com/larksuite/meegle-cli/examples/enterprise-cli/credential"
	_ "github.com/larksuite/meegle-cli/examples/enterprise-cli/governance"
	_ "github.com/larksuite/meegle-cli/examples/enterprise-cli/transport"
)

var version = "1.2.3"

func main() {
	os.Exit(meeglecmd.ExecuteWithVersion(version))
}
```

`version` [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。 [translated text] [translated text] [translated text] [translated text] `RequireCLI`， [translated text] [translated text] `ExecuteWithVersion`， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ； [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] `Execute()`。 [translated text] `go run` [translated text] [translated text] [translated text] [translated text] `dev` [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] `ExecuteWithVersion`。

 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ：

```bash
go build -o corp-meegle ./examples/enterprise-cli

CORP_MEEGLE_HOST=project.example.com \
CORP_MEEGLE_TOKEN=example-token \
CORP_DEVICE_TRUST=trusted \
./corp-meegle workitem get --project-key demo --work-item-id 123
```

 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] MCP [translated text] [translated text] 。MCP [translated text] `tools/list` [translated text] ， [translated text] `metadata.resource` [translated text] `metadata.method` [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ； [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] CLI [translated text] [translated text] fallback [translated text] 。

## 3. Credential： [translated text] [translated text] [translated text] [translated text] token

 [translated text] `credential.Provider`， [translated text] [translated text] [translated text] [translated text] `init` [translated text] [translated text] ：

```go
type provider struct{}

func (provider) Name() string  { return "corp-sso" }
func (provider) Priority() int { return 1 } // [translated text] ； [translated text] [translated text] [translated text] [translated text] ， [translated text] 10

func (provider) ResolveAccount(ctx context.Context) (*credential.Account, error) {
	return &credential.Account{
		Host:        "project.example.com",
		ProfileName: "corp",
	}, nil
}

func (provider) ResolveToken(ctx context.Context, spec credential.TokenSpec) (*credential.Token, error) {
	return &credential.Token{
		Value:  obtainTokenFromEnterpriseSSO(ctx, spec),
		Header: "Authorization",
		Source: "corp-sso",
	}, nil
}

func init() { credential.Register(provider{}) }
```

Provider [translated text] [translated text] [translated text] ：

- `value, nil`： [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] token。
- `nil, nil`： [translated text] ， [translated text] [translated text] [translated text] Provider； [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。
- `nil, error`： [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] SSO [translated text] [translated text] [translated text] 。
- `nil, &credential.BlockError{...}`：Provider [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] 、 [translated text] [translated text] [translated text] 。

Credential [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] Hook。`--help`、`version`、`config`、`completion`、`url`、`extension` [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] Provider； [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] fail-closed。 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] Platform Lifecycle/Policy [translated text] [translated text] [translated text] 。

`Name()` [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] 、 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ； [translated text] [translated text] [translated text] Name/Priority [translated text] [translated text] panic [translated text] [translated text] [translated text] CLI [translated text] fail-closed。Account/Token [translated text] [translated text] [translated text] 30 [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] `ctx`。`Token.Source` [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] `<invalid>`； [translated text] [translated text] `Token.Header` [translated text] [translated text] [translated text] HTTP Header [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] 。

 [translated text] [translated text] [translated text] [translated text] [translated text] ：

```go
return &credential.Token{
	Value:  token,
	Header: "X-Enterprise-Token", // [translated text] [translated text] Authorization: Bearer ...
	Source: "corp-sso",
}, nil
```

 [translated text] CLI [translated text] [translated text] [translated text] [translated text] [translated text] ；MCP [translated text] `tools/list` [translated text] [translated text] `tools/call` [translated text] [translated text] [translated text] [translated text] token， [translated text] [translated text] [translated text] token [translated text] [translated text] 。

 [translated text] [translated text] [translated text] [translated text] Header [translated text] ，CLI [translated text] [translated text] profile headers [translated text] [translated text] `Authorization` [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] Provider [translated text] [translated text] [translated text] Token。

## 4. Transport： [translated text] [translated text] CLI [translated text] [translated text] HTTP [translated text]

Transport [translated text] [translated text] [translated text] 、 [translated text] [translated text] [translated text] [translated text] 、 [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ：

```go
type provider struct{}
type interceptor struct{}

func (provider) Name() string { return "corp-network" }
func (provider) ResolveInterceptor(context.Context) transport.Interceptor {
	return interceptor{}
}

// [translated text] [translated text] / [translated text] [translated text] 。
func (interceptor) PreRoundTrip(req *http.Request) func(*http.Response, error) {
	started := time.Now()
	req.Header.Set("X-Corp-Caller", "meegle-cli")
	host := req.URL.Host // [translated text] [translated text] [translated text] ， [translated text] [translated text] Hook [translated text] [translated text] [translated text] req。
	return func(resp *http.Response, err error) {
		// resp [translated text] [translated text] [translated text] ： [translated text] [translated text] [translated text] /Header， [translated text] Body [translated text] [translated text]
		// http.NoBody， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。
		recordLatency(host, time.Since(started), resp, err)
	}
}

func init() { transport.Register(provider{}) }
```

 [translated text] [translated text] [translated text] ， [translated text] [translated text] `AbortableInterceptor`：

```go
func (interceptor) PreRoundTripE(req *http.Request) (func(*http.Response, error), error) {
	if !strings.HasSuffix(req.URL.Hostname(), ".company.example") {
		return nil, errors.New("destination is outside the corporate network")
	}
	return interceptor{}.PreRoundTrip(req), nil
}
```

V1 [translated text] Transport [translated text] ：

- MCP [translated text] [translated text] `tools/list`。
- MCP [translated text] [translated text] `tools/call`， [translated text] 401 [translated text] [translated text] 。
- OAuth discovery、client [translated text] 、device-code、token exchange [translated text] token refresh。
- [translated text] [translated text] 、 [translated text] [translated text] [translated text] [translated text] HTTP [translated text] 。

 [translated text] [translated text] [translated text] [translated text] Transport Provider； [translated text] [translated text] [translated text] [translated text] [translated text] 。 [translated text] [translated text] CLI [translated text] [translated text] `http.Client`， [translated text] [translated text] [translated text] [translated text] `http.DefaultClient`。Provider、 [translated text] Hook [translated text] [translated text] Hook [translated text] [translated text] [translated text] 30 [translated text] ； [translated text] MCP、OAuth [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] Context [translated text] HTTP Client timeout， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。 [translated text] [translated text] [translated text] [translated text] [translated text] 10 [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] `CheckRedirect` [translated text] [translated text] [translated text] scheme； [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] HTTPS [translated text] [translated text] HTTP。MCP [translated text] Bearer Token [translated text] [translated text] Token Header [translated text] [translated text] [translated text] [translated text] origin [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] Transport Provider [translated text] [translated text] [translated text] [translated text] 、 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] Request， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 、 [translated text] [translated text] [translated text] [translated text] Header；CLI [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] Header [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] Transport。 [translated text] [translated text] [translated text] Credential [translated text] ，Transport [translated text] [translated text] [translated text] [translated text] 、 [translated text] [translated text] [translated text] [translated text] 。 [translated text] `req.Body` [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] ； [translated text] [translated text] Body [translated text] [translated text] `net/http` [translated text] [translated text] `Read/Close` [translated text] ，Context [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] Request。 [translated text] [translated text] [translated text] [translated text] [translated text] 、Header、Trailer、Request [translated text] [translated text] [translated text] ，`Body` [translated text] [translated text] `http.NoBody`，`resp.Request.Context()` [translated text] [translated text] [translated text] [translated text] Hook [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。 [translated text] [translated text] [translated text] [translated text] ，CLI [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。Go [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。

## 5. Platform： [translated text] 、 [translated text] [translated text] [translated text] [translated text]

### 5.1 [translated text] [translated text] [translated text] [translated text]

Observer [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] ：

```go
builder.Observer(platform.Before, "audit-start", platform.All(),
	func(ctx context.Context, in platform.Invocation) {
		log.Printf("start %s", in.Cmd().Path())
	})

builder.Observer(platform.After, "audit-result", platform.All(),
	func(ctx context.Context, in platform.Invocation) {
		log.Printf("finish %s err=%v denied=%t",
			in.Cmd().Path(), in.Err(), in.DeniedByPolicy())
	})
```

### 5.2 [translated text] [translated text]

Wrapper [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ：

```go
builder.Wrap("change-ticket", platform.ByWrite(), func(next platform.Handler) platform.Handler {
	return func(ctx context.Context, in platform.Invocation) error {
		if os.Getenv("CHANGE_TICKET") == "" {
			return &platform.AbortError{
				HookName: "change-ticket",
				Reason:   "write command requires CHANGE_TICKET",
			}
		}
		return next(ctx, in)
	}
})
```

 [translated text] Wrapper [translated text] [translated text] [translated text] [translated text] `next`， [translated text] [translated text] [translated text] Wrapper [translated text] [translated text] [translated text] ； [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] `next` [translated text] ， [translated text] [translated text] Handler [translated text] [translated text] [translated text] [translated text] [translated text] Cobra、IO [translated text] [translated text] [translated text] 。`next` [translated text] [translated text] nil [translated text] [translated text] ， [translated text] Wrapper [translated text] [translated text] [translated text] [translated text] nil， [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。 [translated text] Wrapper [translated text] [translated text] Handler [translated text] [translated text] [translated text] [translated text] `ctx`， [translated text] [translated text] [translated text] `next` [translated text] [translated text] 。

### 5.3 [translated text] [translated text]

 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ：

```go
platform.All()
platform.None()
platform.ByDomain("workitem", "view")
platform.ByCommandPath("workitem/get", "view/**", "workitem/**/get")
platform.ByReadOnly()
platform.ByWrite()
platform.ByExactRisk(platform.RiskHighRiskWrite)
platform.ByIdentity(platform.IdentityUser)

// [translated text] [translated text]
platform.ByDomain("workitem").And(platform.ByWrite())
platform.ByReadOnly().Or(platform.ByCommandPath("version"))
platform.ByCommandPath("auth/**").Not()
```

`*` [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ，`**` [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。

### 5.4 [translated text] [translated text]

```go
builder.
	On(platform.Startup, "startup", func(ctx context.Context, event *platform.LifecycleContext) error {
		return openAuditSink(ctx)
	}).
	On(platform.Shutdown, "shutdown", func(ctx context.Context, event *platform.LifecycleContext) error {
		return closeAuditSink(ctx, event.Err)
	})
```

 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] startup [translated text] shutdown。 [translated text] [translated text] [translated text] /Install [translated text] [translated text] startup Hook [translated text] 2 [translated text] [translated text] [translated text] ：fail-open [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ，fail-closed [translated text] [translated text] [translated text] CLI，Install [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。shutdown [translated text] [translated text] 2 [translated text] ； [translated text] fail-closed shutdown Hook [translated text] [translated text] ， [translated text] Hook [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] 。

### 5.5 Restrict [translated text] [translated text]

 [translated text] Agent [translated text] ：

```go
builder.Restrict(&platform.Rule{
	Name:       "agent-readonly",
	Allow:      []string{"workitem/**", "view/**", "extension/**"},
	Deny:       []string{"workitem/delete"},
	MaxRisk:    platform.RiskRead,
	Identities: []platform.Identity{platform.IdentityUser},
})
```

`extension/**` [translated text] [translated text] [translated text] Allow [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] Diagnostic Commands。Restrict [translated text] [translated text] Diagnostic Commands； [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。

 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ： [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ； [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] 。

 [translated text] [translated text] [translated text] [translated text] [translated text] `read`、`write`、`high-risk-write`。 [translated text] [translated text] [translated text] WBS [translated text] [translated text] [translated text] WBS [translated text] [translated text] [translated text] [translated text] `high-risk-write`； [translated text] MCP [translated text] [translated text] [translated text] [translated text] ， [translated text] `MaxRisk` [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] `AllowUnannotated: true` [translated text] [translated text] 。

 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] Restrict， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。 [translated text] [translated text] [translated text] [translated text] [translated text] Rule， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] Rule； [translated text] Deny [translated text] [translated text] [translated text] [translated text] ， [translated text] Rule [translated text] [translated text] [translated text] Rule [translated text] [translated text] 、 [translated text] [translated text] [translated text] [translated text] 。 [translated text] [translated text] [translated text] fail-closed。

 [translated text] [translated text] `--format json` [translated text] `--format ndjson` [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] envelope， [translated text] [translated text] `CLIENT_COMMAND_DENIED`，`detail` [translated text] [translated text] rule、reason code [translated text] [translated text] policy source， [translated text] Agent [translated text] [translated text] 。CLI App [translated text] [translated text] [translated text] Credential [translated text] Platform [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] envelope， [translated text] [translated text] `CLIENT_CREDENTIAL_RESOLUTION_FAILED` [translated text] `CLIENT_EXTENSION_INSTALL_FAILED`。 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] `CLIENT_EXTENSION_RUNTIME_FAILED`、`CLIENT_EXTENSION_ABORTED`， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。

### 5.6 [translated text] [translated text]

```go
func init() {
	plugin := platform.NewPlugin("corp-governance", "1.0.0").
		RequireCLI(">=1.2.0 <2.0.0").
		FailClosed().
		Observer(platform.After, "audit", platform.All(), observe).
		Wrap("change-ticket", platform.ByWrite(), wrap).
		On(platform.Startup, "startup", onStartup).
		Restrict(readOnlyRule()).
		MustBuild()

	platform.Register(plugin)
}
```

`RequireCLI` [translated text] [translated text] [translated text] [translated text] [translated text] / [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] `1.2.3`、`>=1.2.0 <2.0.0`、`>= 1.2.0, < 2.0.0`。 [translated text] [translated text] [translated text] [translated text] `FailOpen`： [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ；Restrict [translated text] [translated text] `FailClosed`： [translated text] [translated text] CLI [translated text] [translated text] 。 [translated text] Builder [translated text] ， [translated text] `Restrict(...)` [translated text] [translated text] [translated text] fail-closed； [translated text] `Plugin` [translated text] [translated text] [translated text] `Restricts: true` [translated text] `FailurePolicy: FailOpen`，CLI [translated text] [translated text] `Install` [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] 。

## 6. [translated text] [translated text] 、 [translated text] [translated text] [translated text] [translated text]

- `auth`、`config`、`inspect`、`completion`、`url`、`version`、`extension` [translated text] [translated text] [translated text] 。
- [translated text] [translated text] [translated text] MCP `tools/list`， [translated text] `workitem get`、`view list`， [translated text] [translated text] [translated text] 。 [translated text] [translated text] [translated text] `metadata.resource` [translated text] `metadata.method`； [translated text] metadata [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] fallback [translated text] 。
- [translated text] [translated text] [translated text] [translated text] fallback [translated text] [translated text] [translated text] [translated text] ，MCP metadata [translated text] [translated text] [translated text] [translated text] [translated text] `auth status` [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。
- [translated text] [translated text] [translated text] [translated text] wire [translated text] 、 [translated text] 、 [translated text] [translated text] 、Flag [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] ； [translated text] [translated text] [translated text] 、 [translated text] [translated text] [translated text] SDK [translated text] [translated text] 。 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] description [translated text] [translated text] [translated text] [translated text] [translated text] 。
- `meegle --refresh ...` [translated text] [translated text] [translated text] ，Cobra [translated text] [translated text] [translated text] ； [translated text] [translated text] [translated text] [translated text] Platform [translated text] [translated text] [translated text] [translated text] [translated text] 。
- SDK [translated text] [translated text] [translated text] MCP tool [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] [translated text] CLI [translated text] [translated text] [translated text] [translated text] 。

## 7. Diagnostic Commands

 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] token：

```bash
./corp-meegle extension doctor
./corp-meegle extension discovery
./corp-meegle extension credentials
./corp-meegle extension transport
./corp-meegle extension plugins
./corp-meegle extension policy
```

 [translated text] SDK [translated text] `meegle.NewClient(...)` [translated text] `Client`， [translated text] [translated text] [translated text] `client.DiscoveryIssues()` [translated text] [translated text] [translated text] [translated text] [translated text] `Code`、`ToolName` [translated text] `Path`； [translated text] [translated text] [translated text] Token、Header [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] 。

 [translated text] [translated text] ：

```text
credential: corp-sso priority=1
credential-active: not-evaluated token-source=unknown
transport: corp-network status=active hook-timeout=30s tls-downgrade=blocked redirects=10
plugin: corp-governance version=1.0.0 status=active policy=fail-open restricts=false
```

## 8. V1 [translated text] [translated text] [translated text] [translated text]

- [translated text] [translated text] [translated text] [translated text] 、 [translated text] [translated text] [translated text] Go [translated text] 。
- [translated text] [translated text] Meegle [translated text] [translated text] [translated text] 。
- [translated text] [translated text] [translated text] token/ [translated text] [translated text] [translated text] 。
- [translated text] [translated text] E2E [translated text] [translated text] 。
- [translated text] SDK [translated text] [translated text] [translated text] CLI [translated text] 。

 [translated text] [translated text] [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] Credential [translated text] [translated text] [translated text] [translated text] ， [translated text] [translated text] [translated text] V1 [translated text] CLI [translated text] 、Transport [translated text] Platform [translated text] 。
