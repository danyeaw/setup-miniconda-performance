# Setup Miniconda Performance Testing

This project hosts GitHub Actions workflow runs that test configurations of the
setup-miniconda action to find the most optimum configuration for performance.

## Core Setup Performance

| Setup Method | Setup Time |
|--------------|------------|
| Native Runner Conda | 72.8s |
| Setup Runner Conda | 81.8s |
| Latest Miniconda | 97.6s |
| Miniforge | 78s |

Native runner Conda gets setup fastest, but only use it if you
are going to use the test environment.

## Caching Performance

Package Caching:
  Without Cache: 77.9s
  With Cache: 25.1s
Environment Caching:
  First Run: 187.3s
  Second Run: 67.4s
Drive Optimization:
  C: Drive: 120s
  D: Drive: 117.8s

Result: cache packages using the normal cache location.

## Channel Performance

| Channel        | Time (s) | Packages |
|----------------|----------|----------|
| Full Channels  | 223.7      | 384320     |
| Defaults Only  | 161.3      | 34971     |
| Custom Channel | 80.3      | 52     |

Result: Installing from a custom channel can result in a significant speedup.

## Shell Performance

Result: Although there were differences, no shell consistently outperformed the others.

## Solver Optimization

| Solver      | Time (s) |
|-------------|----------|
| Default     | 67.1      |
| Mamba       | 54.7      |
| Mamba v1    | 72.8      |

Result: Mamba is faster than both Conda (libmamba) and Mamba v1.  

## Environment Operations

| Approach | Total Time | Description |
|----------|------------|-------------|
| Environment Update | 144.3s | setup-miniconda + update existing 'test' environment |
| Integrated Setup | 141.4s | setup-miniconda with environment-file parameter |
| Separate Creation | 188.6s | setup-miniconda + conda env create separately |

Result: Avoid creating an environment as a separate step.

## Lockfile Performance

| Approach | Installation Time | Reproducibility | Speed |
|----------|-------------------|------------------|-------|
| Standard environment.yml | 60.5s | Flexible versions | Variable |
| conda-lock lockfile | 53.3s | Exact versions | Consistent |
| pip requirements.txt | 102.9s | Flexible versions | Variable |

Result: With a small number of requirements, there is a small performance
advantage with the lockfile.

## Summary

1. Prefer using Mamba where possible, if you can't, use the Conda
installed on the runner and update the environment called test
2. Create a custom channel to optimize repodata performance
3. Cache packages in the default location
4. Use a lock file
