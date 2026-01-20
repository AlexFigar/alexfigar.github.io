---
author: Alex Figar
title: What 3 Words to Coordinates
date: 2025-11-11
description: A small tool to convert What3Words back to coordinates
tags:
  - Rust
categories:
  - Programming
ShowToc: false
TocOpen: false
Draft: "1"
---
My girlfriend needed to convert a list of What3Words back into coordinates for her Masters so I wrote this little tool... Unfortunately I took too long and she never ended up using it.
<!--more-->

## Installation


## Basic Implementation

```Rust{lineos=true,hl_lines=[11,12,22]}
use curl::easy::Easy;
use std::env;
use std::fs::File;
use std::io::{self, BufRead};
use std::path::Path;

fn main() {
    let _args: Vec<String> = env::args().collect();

    if let Ok(lines) = read_lines(_args[1].to_string()) {
        // Consumes the iterator, returns an (Optional) String
        for line in lines.map_while(Result::ok) {
            println!("{}", line);
        }
    }
}

fn read_lines<P>(filename: P) -> io::Result<io::Lines<io::BufReader<File>>>
where
    P: AsRef<Path>,
{
    let file = File::open(filename)?;
    Ok(io::BufReader::new(file).lines())
}

```
