# SQL dump

Create MySQL/MariaDB/PostgreSQL dumps in Go without external tools.

## MySQL example

```go
package main

import (
	"database/sql"
	"fmt"

	"github.com/grimdork/gmysqldump"
	_ "github.com/go-sql-driver/mysql"
)

func main() {
	// Open connection to database
  username := "your-user"
  password := "your-pw"
  hostname := "your-hostname"
  port := "your-port"
	dbname := "your-db"

  dumpDir := "dumps"  // you should create this directory
  dumpFilenameFormat := fmt.Sprintf("%s-20060102T150405", dbname)   // accepts time layout string and add .sql at the end of file

	db, err := sql.Open("mysql", fmt.Sprintf("%s:%s@tcp(%s:%s)/%s", username, password, hostname, port, dbname))
	if err != nil {
		fmt.Println("Error opening database: ", err)
		return
	}

	// Register database with mysqldump
	dumper, err := mysqldump.Register(db, dumpDir, dumpFilenameFormat)
	if err != nil {
		fmt.Println("Error registering databse:", err)
		return
	}

	// Dump database to file
	resultFilename, err := dumper.Dump()
	if err != nil {
		fmt.Println("Error dumping:", err)
		return
	}
	fmt.Printf("File is saved to %s", resultFilename)

	// Close dumper and connected database
	dumper.Close()
}

```

## PostgreSQL example

Import "github.com/lib/pq" and change the connection string in the example above, then the package handles the rest.

## Selective dump

You may also specify a list of tables to include to the Dump() function:

```go
	dumper.Dump("users", "groups")
```

## Original documentation

[![GoDoc](https://godoc.org/github.com/JamesStewy/go-mysqldump?status.svg)](https://godoc.org/github.com/JamesStewy/go-mysqldump)
