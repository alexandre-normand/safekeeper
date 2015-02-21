safekeeper
==========

`safekeeper` is a command-line tool meant to help maintain your secrets (passwords, client ids/secrets) safe from your source. 

It does a single thing and this is replacing all occurences of keys by the value mapped to the corresponding environment variable. 

It works with [go generate](http://blog.golang.org/generate) to replace tokens in your source code. 

Example
--------

Let's have all of our application secrets in one place. Let's put this in a `secrets` package. We'll need a `<yourname>.go.safekeeper` file that will serve as the template for the generated go code as well as a matching `<yourname>.go` file with the `go:generate` directive. 

`secrets.go`:

```
package secrets
//go:generate safekeeper --output=appsecrets.go --keys=CLIENT_ID,CLIENT_SECRET $GOFILE
```

`secrets.go.safekeeper`:
```
package secrets

// AppSecrets is the source for all application secrets (client ids/secrets/passwords)
type AppSecrets struct {
    ClientId       string
    ClientSecret   string    
}

// NewAppSecrets returns the AppSecrets with all values set
func NewAppSecrets() *AppSecrets {
    appSecrets := new(AppSecrets)
    appSecrets.ClientId = "ENV_CLIENT_ID"
    appSecrets.ClientSecret = "ENV_CLIENT_SECRET"

    return appSecrets
}
```

Since you'd want to avoid committing the generate source with the resolved secrets, you'll want to have the generated file be in your `.gitignore`. We achieve this here by generating the output to a 3rd file called `appsecrets.go` (using the `--output` flag) in the same package. 

Once ready, generate the resolved `appsecrets` by running 
`go generate <path.to.secrets.package>`

I'm currently using this in [glukit](https://github.com/alexandre-normand/glukit) so have a look there for an example of actual integration.

LICENSE
=======
Licensed under MIT license.