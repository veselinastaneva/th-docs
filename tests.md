<h1>My Tests</h1>

{{button: { to: "/{{handler}}/{{teachable}}/deployments", type: "success", size: "small", title: "Create a new serving key" } }}
{{link: { to: "www.sashido.io", title: "Create", target: "_blank" } }}



|Error|Reason|
|-----------|:-------------|
| `MissingTeachableError` | Teachable is required for every prediction |
| `NoThAuthMethodError` | Missing Serving Key param in the initializer |
| `UnsuccessfulRequestError` | General Serving API errors 4xx and 5xx |
| `UnauthorizedError` |  Wrong Serving Key or configuration |
