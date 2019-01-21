# email [![Travis-CI](https://travis-ci.org/scorredoira/email.svg?branch=master)](https://travis-ci.org/scorredoira/email) [![GoDoc](https://godoc.org/github.com/scorredoira/email?status.svg)](http://godoc.org/github.com/scorredoira/email) [![Report card](https://goreportcard.com/badge/github.com/scorredoira/email)](https://goreportcard.com/report/github.com/scorredoira/email)

An easy way to send emails with attachments in Go

**Install**

```bash
go get github.com/number23/email
```

**Add verbose mode**
```bash
$ cp /usr/local/go/src/net/smtp/* $GOPATH/src/sandspace/smtp/
$ vi $GOPATH/src/sandspace/smtp/smtp.go
```
```go
// cmd is a convenience function that sends a command and returns the response
func (c *Client) cmd(expectCode int, format string, args ...interface{}) (int, string, error) {
    if args != nil {
        log.Println("[SEND] ", fmt.Sprintf(format, args...))
    } else {
        log.Println("[SEND] ", format)
    }
    id, err := c.Text.Cmd(format, args...)
    if err != nil {
        return 0, "", err
    }
    c.Text.StartResponse(id)
    defer c.Text.EndResponse(id)
    code, msg, err := c.Text.ReadResponse(expectCode)
    log.Println("[RECIEVE] ", code, " ", msg)
    return code, msg, err
}
```

# Usage

```go
package email_test

import (
	"log"
	"net/mail"
	"net/smtp"

	"github.com/number23/email"
)

func Example() {
	// compose the message
	m := email.NewMessage("Hi", "this is the body")
	m.From = mail.Address{Name: "From", Address: "from@example.com"}
	m.To = []string{"to@example.com"}

	// add attachments
	if err := m.Attach("email.go"); err != nil {
		log.Fatal(err)
	}

	// add headers
	m.AddHeader("X-CUSTOMER-id", "xxxxx")

	// send it
	auth := smtp.PlainAuth("", "from@example.com", "pwd", "smtp.zoho.com")
	if err := email.Send("smtp.zoho.com:587", auth, m); err != nil {
		log.Fatal(err)
	}
}
```

# Html

```go
	// use the html constructor
	m := email.NewHTMLMessage("Hi", "this is the body")
```

# Inline

```go
	// use Inline to display the attachment inline.
	if err := m.Inline("main.go"); err != nil {
		log.Fatal(err)
	}
```
