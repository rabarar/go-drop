dropcam
=======

usage: go get github.com/rabarar/dropcam

Dropcam Library 0.1 (Beta)

Based on the great work by Ryan Galloway (https://github.com/rsgalloway/dropcam), I've adopted this code for go.

http://godoc.org/github.com/rabarar/dropcam

How to use:

package main

import (
        "github.com/rabarar/dropcam"
        "fmt"
        "time"
)
import "os"

const (
        USER = "DROPCAM_USER"
        PASS = "DROPCAM_PASS"
)

func main() {

        u := os.Getenv(USER)
        p := os.Getenv(PASS)

        if u == "" || p == "" {
                fmt.Printf("need to set both %s and %s\n", USER, PASS)
                return
        }
        
        d, err := new(dropcam.Dropcam).Init(u, p)
        if err != nil {
                fmt.Printf("failed to Init Dropcam Credentials: %s\n", err)
                os.Exit(1)
        }

        c, err := d.Cameras()
        if err != nil {
                fmt.Printf("failed to Get Cameras: %s\n", err)
                os.Exit(1)
        }

        for j, owned := range c.Cam {
                fmt.Printf("%d: %s\n", j, owned.Title)
        }

        // Need to create a directory in the cwd called "images"
        // infinite loop - and  every 5 seconds write the images into the images file
        //
        
        for {
                fmt.Printf("***** GETTING Image **** \n")
                for i, o := range c.Cam {
                        fn := "./images/img-" + fmt.Sprintf("%d-", i) + fmt.Sprintf("%d", time.Now().Unix())
                        err = c.SaveImage(&o, fn, 720, time.Now())
                        if err != nil {
                                fmt.Printf("error saving image %d\n", i)
                        }
                        fmt.Printf("saved image %s\n", fn)
                }
                time.Sleep(5 * time.Second)
        }
}

Still need to add: Events and MediaStreaming
