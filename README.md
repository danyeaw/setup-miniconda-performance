# Setup Miniconda Performance Testing

## 🎯 Testing

### 1. Bundled vs Latest Miniconda
Tests performance difference between:
- **Bundled**: Default `$CONDA` on Windows runners (no `miniconda-version` specified)
- **Latest**: Upgrading to `miniconda-version: "latest"`

**Process**: Create environment → Test it works → Delete environment → Report timings

### 2. Caching Performance  
Tests caching performance impact:
- **Without Cache**: Fresh downloads every time
- **With Cache**: Uses `actions/cache` for conda packages

## 🔬 Optimization Proof Testing

These run **separately** to prove they actually improve performance:

### 3. Solver Optimization Proof
Tests if different solvers are actually faster:
- **Default**: Standard conda solver (baseline)
- **Libmamba**: Fast C++ solver  
- **Mamba**: Complete mamba replacement

### 4. Drive Optimization Proof
Tests if D: drive caching is actually faster:
- **C: Drive**: Standard `C:\conda_pkgs_dir`
- **D: Drive**: Optimized `D:\conda_pkgs_dir` with `enableCrossOsArchive`

## 📊 How to Interpret Results

### Step 1: Check Original Request Results
```
Job: bundled-vs-latest
- Look for "CORE PERFORMANCE RESULTS" 
- Compare creation + deletion times
- Determine if "latest" is worth the download time

Job: caching-performance  
- Look for "CACHE PERFORMANCE RESULTS"
- Compare "with-cache" vs "without-cache" times
- Quantify cache benefits for your use case
```

### Step 2: Evaluate Optimizations
```
Job: solver-optimization-proof
- Compare "default" vs "libmamba" vs "mamba" times
- Only use if you see meaningful improvement

Job: drive-optimization-proof  
- Compare "c-drive" vs "d-drive" times
- Only use D: drive if it's actually faster
```

## ✅ Using Results in Your Workflows

### Standard:
```yaml
- uses: conda-incubator/setup-miniconda@v3
  with:
    miniconda-version: "latest"  # Only if test proves this helps
    
- name: Cache conda packages
  uses: actions/cache@v3
  with:
    path: ~/conda_pkgs_dir  # Only if test proves caching helps
```

### Potential Optimizations:
```yaml
- uses: conda-incubator/setup-miniconda@v3
  with:
    conda-solver: libmamba  # Only if tests prove it's faster
    
- name: Cache conda packages
  uses: actions/cache@v3
  with:
    path: D:\conda_pkgs_dir  # Only if tests prove D: is faster
    enableCrossOsArchive: true
```

## 🚀 Running the Tests

1. **Manual Trigger**: Go to Actions tab → Select workflow → "Run workflow"
2. **Auto Trigger**: Push changes to main branch that modify the workflow file

## 📈 Expected Performance Patterns

**Bundled vs Latest**:
- Bundled: Faster initial setup (no download)
- Latest: May be slower initially but could have better solvers

**Caching**:
- First run: Slower (cache miss, building cache)
- Second+ runs: Should be significantly faster (cache hit)

**Solver Optimizations**:
- Default: Baseline performance
- Libmamba: Should be faster for complex environments
- Mamba: Should be fastest but requires conda-forge channel

**Drive Optimizations**:
- C: Drive: Standard performance  
- D: Drive: May be faster due to less system I/O contention

## 📋 Test Environment

**Platform**: Windows-latest runners only  
**Timing Method**: PowerShell `[System.DateTime]::Now.Ticks` for accurate Windows timing  
**Cache Locations**: Standard paths vs D: drive optimization

## 📄 Results Format  

Each test outputs timing results:
```
=== CORE PERFORMANCE RESULTS ===
Setup Type: bundled
Creation Time: 45.2 seconds  
Deletion Time: 3.1 seconds
Total Time: 48.3 seconds
```
