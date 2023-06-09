+++
title = "SQLHammer: A SQL data generator with math"
date = "2023-06-09"
author = "Ethan Hanlon"
cover = "https://firebasestorage.googleapis.com/v0/b/hanlon-blog.appspot.com/o/BlogImages%2FScreenshot%202023-06-09%20at%2012.08.23%20PM.png?alt=media"
description = ""
+++

**SQLHammer Link:** https://github.com/OccultSlolem/SQLHammer

Recently I've been working on some AI-based technology for work. Of course, in order to generate an AI model, you need a whole ton of data to base it off of. The tough thing was that, given how busy we were trying to move product, I didn't have enough time to build that dataset. So I figured, let's just build the functionality around some dummy data and build the product around that. I needed a lot of data quickly, so instead of spending hours hammering out thousands of INSERT INTO statements I decided to spend hours building **SQLHammer**.

SQLHammer is a command line tool written in Python that can take a given template and structure a SQLite database around it. The real kicker is that you can insert mathematical models and introduce random variance into your data. The template and equations are fed in through a JSON file and the data is processed and inserted into the database. The tool is designed to be as flexible as possible, so you can use it for many different kinds of data.

Here's an example ``settings.jsonc`` (jsonc is just JSON with comments permitted) that would generate a table with y and x columns corresponding to a parabolic arc:

```json
{
    "database": "para.db",
    "table": "para",
    "columns": ["y", "x"],
    "column_types": ["REAL", "REAL"],
    "iterations": 1,
    "constants": [], // none needed here
    "equations": [
        "PARABOLA": {
            "variables": ["x"],
            "equation": "x^2",
            "variance": 0.0
        }
    ],

    "template": {
        "y": "EQUATION_PARABOLA",
        "x": "INCREMENT_FROM_-10"
    }
}
```

If that seems kinda arcane, I totally get it since I primarily made it for the purpose of my project. Still, the basic points are something like:

- We write it into a SQLite file called `para.db`, inserting all data into the `para` table.
- The `para` table has columns `y` and `x`, both of type `REAL`.
- We define an equation called `PARABOLA` that takes in a variable `x` and returns `x^2` with no variance.
- We define a template that inserts the `PARABOLA` equation into the `y` column and increments the `x` column from -10.

If we had given some variance, it would've added a random value plus or minus the variance amount for each step.

Running the program with this setup, we are first asked what the lower and upper bounds of the data are, as you can see in the screenshot. Running it from -10 to 10, we get a dataset that looks something like this:

```bash
$ sqlite3 para.db
sqlite> SELECT * FROM para;

y           x
----------  ----------
100.0       -10.0
81.0        -9.0
64.0        -8.0
49.0        -7.0
36.0        -6.0
25.0        -5.0
16.0        -4.0
9.0         -3.0
4.0         -2.0
1.0         -1.0
0.0         0.0
1.0         1.0
4.0         2.0
9.0         3.0
16.0        4.0
25.0        5.0
36.0        6.0
49.0        7.0
64.0        8.0
81.0        9.0
100.0       10.0
```

Kinda neat, huh?

There's still a good amount of jank ~~and I'm totally gonna get around to writing the tests some day~~ but I'm pretty happy with how it turned out. I'm hoping to add more features to it in the future. Currently only SQLite is supported, but adding support for other databases like MySQL or Postgres would be kinda cool. I've got this tool hammered out for my own purposes already so it's not at the top of the agenda, but definitely something I'd like to get around to some time.

If you're interested in using SQLHammer, you can find it on [GitHub](https://github.com/OccultSlolem/SQLHammer). Feel free to open an issue or pull request if you have any suggestions or bug fixes. I'm always happy to make my code more useful for others :)
