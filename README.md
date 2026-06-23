# Filesystem Traversal Benchmark Lab

Benchmark comparing Python filesystem traversal methods based on HN discussion about Python 3.5's `os.scandir()`.

## Quick Start

```bash
git clone https://github.com/necat101/fs-traversal-benchmark.git
cd fs-traversal-benchmark
python3 benchmarks/benchmark.py
```

## Overview

This benchmark compares different Python methods for traversing directory trees:
- `os.walk()` - Traditional recursive walker
- `os.scandir()` - New in Python 3.5 (PEP 471)
- `os.listdir()` + `os.stat()` - Old-school approach
- `pathlib.Path.rglob()` - Modern OO API
- `glob.iglob()` - Pattern-based matching

Based on HN discussion: https://news.ycombinator.com/item?id=9845017

## Test Results

**System**: Linux 6.17.0, Python 3.12.3  
**Corpus**: 582 files, 30 directories, 339.5 KB

| Method | Full Traversal | With Filter | Notes |
|--------|---------------|-------------|-------|
| os.scandir | **0.0092s** | **0.0104s** | Fastest - uses DirEntry cache |
| os.walk | 0.0121s | 0.0125s | Uses scandir internally (3.5+) |
| pathlib | 0.0651s | 0.0269s | 5x slower, nicer API |
| glob | 0.0798s | 0.0813s | 6-8x slower |
| os.listdir | 0.1483s | 0.1230s | **12x slower** - extra stat calls |

**Key Finding**: `os.scandir()` validates PEP 471 claims - significantly faster by avoiding unnecessary `stat()` calls.

## HN Discussion Context

Python 3.5 (2015) added `os.scandir()` via PEP 471. HN users debated:
- Whether it justified upgrading from Python 2.7/3.4
- If the speedup mattered for real-world code
- API elegance vs raw performance tradeoffs
- The fact that `os.walk()` got faster "for free" in 3.5+

**Consensus**: For code that walks large directory trees, the speedup is significant. For most applications, the nicer `pathlib` API is worth the small performance cost.

## Documentation

- **HN Thread**: https://news.ycombinator.com/item?id=9845017
- **PEP 471**: https://peps.python.org/pep-0471/
- **Python 3.5 Whats New**: https://docs.python.org/3.5/whatsnew/3.5.html
- **os.scandir docs**: https://docs.python.org/3/library/os.html#os.scandir

## License

MIT
