# entimport

> **🔧 Maintained Fork**: This is a community-maintained version of the original [ariga/entimport](https://github.com/ariga/entimport) project. The upstream repository has not been actively maintained, so this fork provides continued development, bug fixes, and feature updates.

`entimport` is a tool for creating [Ent](https://entgo.io/) schemas from existing SQL databases. Currently, `MySQL`
and `PostgreSQL` are supported. The tool can import to [ent schema](https://entgo.io/docs/schema-def) any number of
tables, including relations between them.

## Why This Fork Exists

The original [ariga/entimport](https://github.com/ariga/entimport) project has been inactive for an extended period, with no recent updates or maintenance. This community-maintained fork was created to:

- **Continue Development**: Provide ongoing maintenance and updates to keep the tool functional with the latest Go and Ent versions
- **Bug Fixes**: Address issues and bugs that have been reported but not resolved in the upstream repository  
- **Community Support**: Offer responsive support and accept community contributions
- **Feature Updates**: Add new features and improvements based on community needs

We are committed to maintaining this project and keeping it up-to-date with the evolving Go and Ent ecosystems.

## Installation

### Setup A Go Environment

If your project directory is outside [GOPATH](https://github.com/golang/go/wiki/GOPATH) or you are not familiar with
GOPATH, setup a [Go module](https://github.com/golang/go/wiki/Modules#quick-start) project as follows:

```shell
go mod init <project>
```

### Install ent

```shell
go install entgo.io/ent/cmd/ent
```

After installing `ent` codegen tool, you should have it in your `PATH`. If you don't find it your path, you can also
run: `go run entgo.io/ent/cmd/ent <command>`

### Create Schema Directory

Go to the root directory of your project, and run:

```shell
ent init
```

The command above will create `<project>/ent/schema/` directory and the file inside `<project>/ent/generate.go`

### Importing a Schema

Installing and running `entimport`

```shell
go run github.com/qwenode/entimport/cmd/entimport
```

- For example, importing a MySQL schema with `users` table:

```shell
go run github.com/qwenode/entimport/cmd/entimport -dsn "mysql://root:pass@tcp(localhost:3308)/test" -tables "users"
```

The command above will write a valid ent schema into the directory specified (or the default `./ent/schema`):

```
.
├── generate.go
└── schema
    └── user.go

1 directory, 2 files
```

### Code Generation:

In order to [generate](https://entgo.io/docs/code-gen) `ent` files from the produced schemas, run:

```shell
go run -mod=mod entgo.io/ent/cmd/ent generate ./schema

# OR `ent` init:

go generate ./ent
```

If you are not yet familiar with `ent`, you can also follow
the [quick start guide](https://entgo.io/docs/getting-started).

## Usage

```shell
entimport  -h
```

```
Usage of ./entimport:
  -dsn string
        data source name (connection information), for example:
        "mysql://user:pass@tcp(localhost:3306)/dbname"
        "postgres://user:pass@host:port/dbname"
  -schema-path string
        output path for ent schema (default "./ent/schema")
  -tables value
        comma-separated list of tables to inspect (all if empty)
```

## Examples:

1. Import ent schema from Postgres database

> Note: add search_path=foo if you use non `public` schema.

```shell
go run github.com/qwenode/entimport/cmd/entimport -dsn "postgres://postgres:pass@localhost:5432/test?sslmode=disable" 
```

2. Import ent schema from MySQL database

```shell
go run github.com/qwenode/entimport/cmd/entimport -dsn "mysql://root:pass@tcp(localhost:3308)/test"
```

3. Import only specific tables:

> Note: When importing specific tables:  
> if the table is a join table, you must also provide referenced tables.  
> If the table is only one part of a relation, the other part won't be imported unless specified.   
> If the `-tables` flags is omitted all tables in current `database schema` will be imported

```shell
go run github.com/qwenode/entimport/cmd/entimport -dsn "..." -tables "users,user_friends" 
```

4. Import to another directory:

```shell
go run github.com/qwenode/entimport/cmd/entimport -dsn "..." -schema-path "/some/path/here"
```

## Future Work

- Index support (currently Unique index is supported).
- Support for all data types (for example `uuid` in Postgres).
- Support for Default value in columns.
- Support for editing schema both manually and automatically (real upsert and not only overwrite)
- Postgres special types: postgres.NetworkType, postgres.BitType, *schema.SpatialType, postgres.CurrencyType,
  postgres.XMLType, postgres.ArrayType, postgres.UserDefinedType.

### Known Caveats:

- Schema files are overwritten by new calls to `entimport`.
- There is no difference in DB schema between `O2O Bidirectional` and `O2O Same Type` - both will result in the same
  `ent` schema.
- There is no difference in DB schema between `M2M Bidirectional` and `M2M Same Type` - both will result in the same
 `ent` schema.
- In recursive relations the `edge` names will be prefixed with `child_` & `parent_`.
- For example: `users` with M2M relation to itself will result in:

```go
func (User) Edges() []ent.Edge {
return []ent.Edge{edge.To("child_users", User.Type), edge.From("parent_users", User.Type)}
}
```

## License

This project uses a dual licensing approach:

- **Original Code**: The original entimport code from the upstream repository (ariga/entimport) is licensed under the Apache License 2.0. See the [LICENSE](LICENSE) file for details.

- **Fork-Specific Contributions**: New code, modifications, and contributions specific to this fork are licensed under the MIT License. See the [LICENSE-MIT](LICENSE-MIT) file for details.

This dual licensing approach ensures compatibility with the original Apache 2.0 licensed code while providing flexibility for new contributions to the maintained fork. When contributing to this project, new files and significant modifications will be licensed under MIT, while preserving the Apache 2.0 license for existing upstream code.

## Feedback & Support

For discussion and support with this maintained fork:

- **Issues & Bug Reports**: [Open an issue](https://github.com/qwenode/entimport/issues/new) on this repository
- **Feature Requests**: [Submit a feature request](https://github.com/qwenode/entimport/issues/new) with the enhancement label
- **Discussions**: Use [GitHub Discussions](https://github.com/qwenode/entimport/discussions) for general questions and community support

For historical context or to reference original discussions, you can also check the [original repository](https://github.com/ariga/entimport), though active support is provided through this fork.
