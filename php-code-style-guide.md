layout: page
title: "PHP Code Style Guide"
permalink: /php-code-style-guide

# Index
- [Overview](#overview)
- [Control Structures](#control-structures)
    - [if elseif else](#if-elseif-else)

# Overview

This document aims to create a pattern to code, reducing cognitive friction when scanning code from different authors. It does so by enumerating a shared set of rules and expectations about how to format PHP code.

This page uses as base the PSR-1 and PSR-2 from the PHP Standards Recommendations.

# Control Structures

The general style rules for control structures are as follows:

- There MUST be one space after the control structure keyword
- There MUST NOT be a space after the opening parenthesis
- There MUST NOT be a space before the closing parenthesis
- There MUST be one space between the closing parenthesis and the opening brace
- The structure body MUST be indented once
- The body MUST be on the next line after the opening brace
- The closing brace MUST be on the next line after the body

The body of each structure MUST be enclosed by braces. This standardizes how the structures look and reduces the likelihood of introducing errors as new lines get added to the body.

## if elseif else

An ```if``` structure looks like the following. Note the placement of parentheses, spaces, and braces; and that ```else``` and ```elseif``` are on the same line as the closing brace from the earlier body.

```
<?php

if ($expr1) {
    // if body
} elseif ($expr2) {
    // elseif body
} else {
    // else body;
}
```

The keyword ```elseif``` SHOULD be used instead of ```else if``` so that all control keywords look like single words.

Expressions in parentheses MAY be split across multiple lines, where each subsequent line is indented at least once. When doing so, the first condition MUST be on the next line. The closing parenthesis and opening brace MUST be placed together on their own line with one space between them. Boolean operators between conditions MUST always be at the beginning or at the end of the line, not a mix of both.

```
<?php

if (
    $expr1
    && $expr2
) {
    // if body
} elseif (
    $expr3
    && $expr4
) {
    // elseif body
}
```