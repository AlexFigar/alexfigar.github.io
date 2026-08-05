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
Draft: "0"
---
My girlfriend needed to convert a list of What3Words addresses back into coordinates for her Masters so I wrote this little tool that converts What3Words addresses to latitude/longitude coordinates and vice versa.
<!--more-->

## The API

I started by visiting [what3words.com](https://what3words.com/) and doing a cheeky 'f12' to find out what publicly accessible API endpoint they're using for their webpage.

![](/3W2Coords/f12.png)

Using curl to confirm:
``` bash
curl -i -H "Accept: application/json" "https://mapapi.what3words.com/api/convert-to-coordinates?words=prices.slippery.traps&format=json"
```

Returns this json with our desired coordinates
``` json{lineos=true,hl_lines=[8]}
{
  "country": "FR",
  "square": {
    "southwest": { "lng": 2.294453, "lat": 48.858344 },
    "northeast": { "lng": 2.294494, "lat": 48.858371 }
  },
  "nearestPlace": "Paris",
  "coordinates": { "lng": 2.294473, "lat": 48.858358 },
  "words": "prices.slippery.traps",
  "language": "en",
  "map": "https:\/\/w3w.co\/prices.slippery.traps"
}


```

Confirmed the 2 API endpoints I was after

- Words --> Coords: `https://mapapi.what3words.com/api/convert-to-coordinates`
- Coords --> Words: `https://mapapi.what3words.com/api/convert-to-3wa`

## Implementation

``` rust
use reqwest::blocking::Client;
use serde::Deserialize;
use std::fs::File;
use std::io::{BufRead, BufReader, Write};

struct ApiResponse {
    coordinates: Coordinates,
}

struct Coordinates {
    lat: f64,
    lng: f64,
}

fn lookup_words(client: &Client, words: &str) -> (f64, f64) {
    let url = format!(
        "https://mapapi.what3words.com/api/convert-to-coordinates?words={}&format=json",
        words
    );
    let response = client
        .get(&url)
        .header("Accept", "application/json")
        .send()
        .expect("Failed to send request");
    let body = response.text().expect("Failed to get response body");
    let api_response: ApiResponse =
        serde_json::from_str(&body).expect("Failed to parse JSON response");
    (api_response.coordinates.lat, api_response.coordinates.lng)
}

fn main() {
    let args: Vec<String> = std::env::args().collect();

    let (input, output_to_file, output_path) = match args.len() {
        2 => (args[1].clone(), false, String::new()),
        3 => (args[1].clone(), true, args[2].clone()),
        _ => {
            eprintln!("Usage: {} <input> [output_file]", args[0]);
            eprintln!("  <input> can be:");
            eprintln!("    - A file path (one what3words per line)");
            eprintln!("    - A single set of three words (e.g. prices.slippery.traps)");
            eprintln!("  [output_file] is optional - if omitted, output goes to stdout");
            std::process::exit(1);
        }
    };

    let client = Client::new();

    let words_list: Vec<String> =
        if input.contains('.') && !input.contains('/') && !input.contains('\\') {
            vec![input.clone()]
        } else {
            let input_file = File::open(&input).expect("Failed to open input file");
            let reader = BufReader::new(input_file);
            reader
                .lines()
                .map(|l| l.unwrap().trim().to_string())
                .filter(|w| !w.is_empty())
                .collect()
        };

    let mut output_file =
        output_to_file.then(|| File::create(&output_path).expect("Failed to create output file"));

    for words in words_list {
        let (lat, lng) = lookup_words(&client, &words);
        let output_line = format!("{},{},{}", words, lat, lng);

        if let Some(ref mut file) = output_file {
            file.write_all(format!("{}\n", output_line).as_bytes())
                .expect("Failed to write to output file");
        } else {
            println!("{}", output_line);
        }
    }

    if output_to_file {
        println!("Output written to {}", output_path);
    }
}
```

Up to date code available at https://github.com/AlexFigar/3W2Coords 

## Installation

```bash
git clone https://github.com/AlexFigar/3W2Coords.git
./build.sh
```

Or manually:
```bash
cargo build --release
```

The compiled binary will be at: `target/release/3W2Coords`

## Usage
```bash
# Single what3words to coordinates
./3W2Coords prices.slippery.traps

# Coordinates to what3words (reverse lookup)
./3W2Coords "48.858358,2.294473" --reverse

# File with multiple locations
./3W2Coords test.txt

# Specify output file
./3W2Coords test.txt output.txt

# Output format options
./3W2Coords prices.slippery.traps -f text   # default: words,lat,lng
./3W2Coords prices.slippery.traps -f csv    # comma-separated
./3W2Coords prices.slippery.traps -f json   # JSON array
```

## Options
```
-f, --format <FORMAT>  Output format: text, csv, json [default: text]
-r, --reverse       Coordinate to 3 words (reverse lookup)
-h, --help         Print help
-V, --version      Print version
```

## Output Format

**text/csv** (default):
```
words,lat,lng
prices.slippery.traps,48.858358,2.294473
```

**json**:
```json
[{"lat":48.858358,"lng":2.294473,"words":"prices.slippery.traps"}]
```

