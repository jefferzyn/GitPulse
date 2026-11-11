# GitPulse

[![CI](https://github.com/YOUR-USERNAME/gitpulse/actions/workflows/ci.yml/badge.svg)](https://github.com/YOUR-USERNAME/gitpulse/actions)
![Python](https://img.shields.io/badge/python-3.9%2B-blue)
![License](https://img.shields.io/badge/license-MIT-green)

**Understand a codebase's health from its git history.** GitPulse is a zero-dependency
command-line tool that turns `git log` into actionable insight: who owns the code, which files
change the most, and when the team is actually working.

## Features

- **Bus factor**: how many people account for more than half of all commits
- **Hotspots**: the files that change most often (prime candidates for refactoring)
- **Contributor ranking** by commits and lines changed
- **Activity heatmap** (weekday x hour) rendered right in your terminal
- **Output formats**: plain text, Markdown (paste into a PR or wiki) or JSON (pipe into other tools)
- No runtime dependencies, just Python 3.9+ and `git`

## Installation

```bash
git clone https://github.com/YOUR-USERNAME/gitpulse.git
cd gitpulse
pip install -e .
```

## Usage

```bash
gitpulse                          # analyze the current repo
gitpulse ~/projects/my-app -n 5   # top 5 per ranking
gitpulse --since "6 months ago"   # limit the time window
gitpulse -f markdown > REPORT.md  # export a Markdown report
gitpulse -f json | jq .hotspots   # machine-readable output
```

### Example output

```text
GitPulse report
===============

Summary
-------
Commits:       7
Authors:       2
Bus factor:    1

Hotspots (most frequently changed files)
----------------------------------------
1. app.py: 6 commits, 6 lines
2. util.py: 1 commits, 1 lines

Activity heatmap (weekday x hour)
---------------------------------
     0   2   4   6   8   10  12  14  16  18  20  22
Mon                              ██
Tue        ██            ██
Wed                        ██
```

## How it works

GitPulse runs `git log --numstat` once, parses it into typed `Commit` objects
(`analyzer.py`), computes metrics as small pure functions, and hands the results to a
renderer (`report.py`). The CLI (`cli.py`) is a thin wrapper, which keeps the core easy to
test and reuse as a library:

```python
from gitpulse.analyzer import read_log, parse_log, bus_factor

commits = parse_log(read_log("."))
print(bus_factor(commits))
```

## Development

```bash
pip install -e ".[dev]"
ruff check .
pytest
```

CI runs linting and tests on Python 3.9 to 3.12 for every push and pull request.

## Roadmap

- [ ] Per-directory ownership breakdown
- [ ] Commit-size distribution and "big bang commit" detection
- [ ] HTML report with charts
- [ ] Publish to PyPI

## License

MIT, see [LICENSE](LICENSE).
