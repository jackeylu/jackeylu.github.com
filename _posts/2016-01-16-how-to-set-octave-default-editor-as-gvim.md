How to set the default editor of Octave 4.0 as gvim.
================================

In octave 4.0, the method of changing default editor is changed.

In octave 4.0, we have to use the new method EDITOR() to get or set the default editor.

In the octaverc or .octaverc file, add such command,

```
## set the default editor as vim
EDITOR("/path/to/your/vim --servername ocatve-editor --remote-tab-silent %s"
```

You NEED TO SET THE ABSOLUTE PATH IN WINDOWS, to create the right child process.


