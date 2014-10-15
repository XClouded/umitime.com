title: Mac sublime的快捷键设置
tags:
  - 拓展
date: 2014-01-08 00:45:57
---

<pre>
[
    { "keys": ["super+b"], "command": "toggle_side_bar" },
    { "keys": ["super+right"], "command": "move_to", "args": { "to": "eol" } }, 
    { "keys": ["super+left"], "command": "move_to", "args": { "to": "bol" } },
    { "keys": ["super+u"], "command": "upper_case" },
    { "keys": ["super+l"], "command": "lower_case" }
    // exchange command+f to command+e
    // { "keys": ["super+e"], "command": "show_panel", "args": {"panel": "find"} },
    // { "keys": ["super+f"], "command": "slurp_find_string", "args": {"panel": "find"} }
]
</pre>