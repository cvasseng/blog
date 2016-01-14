title: Setting up Visual Studio Code with Make
categories:
- programming
- howto
----

A couple of months ago, I switched from Sublime - which I had been using for quite some time - to [Visual Studio Code](https://code.visualstudio.com/).
Most of my work-related coding is web-oriented these days, with lots of node.js, less, and html code.
The support for those in VSC is excellent. Node debugging for instance works right out of the box, and is tightly integrated with the editor. It has a few shortcomings (like missing hotkeys for selecting the current/next word, or jumping to the next word), but the benefits so far outweighs the drawbacks.

For my own projects, I'm mostly coding in C/C++. I like to use the same editor for all my projects, and so I've ended up writing my C/C++ code in VSC as well. I tend to use makefiles, mostly because for smaller projects it's quick to set up (at least when I only need it to work on my setup). Up until now I've been alt-tabbing to a console window to compile and run my projects. I then realized that there's gotta be a better way, and sure enough; there is. VSC supports custom [Tasks](https://code.visualstudio.com/Docs/editor/tasks), which can be used to run any shell command from within the editor. It can also be used to pattern match output of these commands, to e.g. highlight errors/warnings directly in the editor. 

Anyway - I adapted one of the task examples from the Visual Studio Code documentation to use make for building/testing. Maybe it's useful for someone else also, so here it is:
    
    
    {
        "version": "0.0.1",
        "command": "make",
        "tasks": [
          {
            "taskName": "rebuild",
            "isBuildCommand": true,
            "problemMatcher": {
                "owner": "cpp",
                "fileLocation": ["relative", "${workspaceRoot}"],
                "pattern": {
                    "regexp": "^(.*):(\\d+):(\\d+):\\s+(warning|error|notice):\\s+(.*)$",
                    "file": 1,
                    "line": 2,
                    "column": 3,
                    "severity": 4,
                    "message": 5
                }
            }
          },
          {
            "taskName": "run",
            "isTestCommand": true,
            "showOutput": "always"
          }
      ]
    }

Put the above JSON in `.settings/` in your project directory. It's set up so that the VSC build command (`cmd+shift+b` on OSX) runs `make rebuild`, and the VSC test command (`cmd+shift+t` on OSX) runs `make run`.
So just change `taskName` to whatever is appropriate for your makefiles, and you should be good to go.

The task also contains a pattern matcher which will underline errors/warnings in your source files (if any) once you run the build task.