# Complete Guide: Create CI/CD Pipeline with GitHub UI

## Prerequisites

Before starting, make sure you have these files in your GitHub repository:
- **Your Python code file** (e.g., `my_functions.py`)
- **Your test file** (e.g., `test_my_function.py`)

## Step 1: Create requirements.txt

1. **Go to your GitHub repository** in your browser
2. **Click "Add file"** → **"Create new file"**
3. **Name the file**: `requirements.txt`
4. **Add this content**:
   ```
   pytest>=7.0.0
   pytest-cov>=4.0.0
   ```
5. **Scroll down** and click **"Commit new file"**

## Step 2: Create the CI/CD Pipeline Using GitHub UI

1. **Go to your repository on GitHub**
2. **Click "Add file"** → **"Create new file"**
3. **Type `.github/workflows/ci.yml` as the filename** (this creates the folders automatically)
4. **Copy and paste this YAML content** into the file:

```yaml
name: Python CI/CD Pipeline

# When to run this pipeline
on:
  push:
    branches: [ main, master ]
  pull_request:
    branches: [ main, master ]

jobs:
  test:
    runs-on: ubuntu-latest
    
    strategy:
      matrix:
        python-version: [3.8, 3.9, '3.10', '3.11']
    
    steps:
    # Get the code from GitHub
    - name: Checkout repository
      uses: actions/checkout@v4
    
    # Set up Python environment
    - name: Set up Python ${{ matrix.python-version }}
      uses: actions/setup-python@v4
      with:
        python-version: ${{ matrix.python-version }}
    
    # Install testing tools
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install pytest pytest-cov
        if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
    
    # Run your tests
    - name: Run tests with pytest
      run: |
        pytest test_my_function.py -v
    
    # Get test coverage (optional)
    - name: Run tests with coverage
      run: |
        pytest test_my_function.py --cov=my_functions --cov-report=term-missing
```

5. **Scroll down and click "Commit new file"**

### Alternative Method: Using Actions Tab

1. **Go to the "Actions" tab** in your repository
2. **Find "Python application"** in the suggested workflows
3. **Click "Configure"** on the Python application template
4. **Replace ALL the content** in the editor with the same YAML above
6. **Click "Commit changes"** (top right, green button)
7. **Add a commit message** like "Add CI/CD pipeline"
8. **Click "Commit changes"** again

### Method B: Create Workflow File Directly

1. **Go to your repository main page**
2. **Click "Add file"** → **"Create new file"**
3. **Type the filename**: `.github/workflows/ci.yml`
4. **Paste the same YAML content** from Method A
5. **Click "Commit new file"**

## Step 3: Watch Your Pipeline Run

1. **Go to the "Actions" tab** immediately after committing
2. **You should see**: "Python CI/CD Pipeline" in the left sidebar
3. **Click on the latest workflow run** (should show "in progress" with a yellow circle)
4. **Watch the steps execute**:
   - ✅ Checkout repository
   - ✅ Set up Python (4 different versions)
   - ✅ Install dependencies
   - ✅ Run tests with pytest
   - ✅ Run tests with coverage

## Step 4: Understanding the Results

### ✅ Success (Green Checkmark):
- All tests passed on all Python versions
- Your code is working correctly
- Safe to deploy or merge

### ❌ Failure (Red X):
- One or more tests failed
- Click on the failed run to see details
- Fix the issue and push again

### ⚠️ In Progress (Yellow Circle):
- Pipeline is currently running
- Wait for it to complete

## Step 5: Test Your Pipeline with Changes

1. **Make a change** to your Python file (add a new function)
2. **Add a test** for the new function in your test file
3. **Commit and push** the changes
4. **Watch the pipeline run automatically** and test your new code!

## Common Issues and Solutions

### Issue 1: "No module named 'pytest'"
**Solution**: Make sure you created `requirements.txt` with `pytest>=7.0.0`

### Issue 2: "No tests ran / No tests found"
**Solution**: 
- Make sure your test file starts with `test_`
- Make sure your test functions start with `test_`
- Check that your test file is in the repository root

### Issue 3: Import errors in tests
**Solution**: Make sure your test file imports correctly:
```python
from my_functions import add, multiply  # Replace with your actual function names
```

### Issue 4: Pipeline doesn't run
**Solution**:
- Check that your main branch is named `main` or `master`
- Make sure the workflow file is at `.github/workflows/ci.yml`
- Verify the YAML indentation is correct

### Issue 5: Git sync issues when pushing locally
**Solution**:
```bash
git pull origin main
# If you get a merge editor, press Esc, then type :wq, then press Enter
git push origin main
```

## Advanced: Customizing Your Pipeline

### Test Only Specific Files:
Change the pytest command to:
```yaml
pytest test_specific_file.py -v
```

### Test All Files:
Change the pytest command to:
```yaml
pytest -v  # Tests all files starting with test_
```

### Add Code Quality Checks:
Add this step to your workflow:
```yaml
- name: Lint with flake8
  run: |
    pip install flake8
    flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics
```

### Add More Python Versions:
Change the matrix to:
```yaml
python-version: [3.7, 3.8, 3.9, '3.10', '3.11', '3.12']
```

## What Happens Automatically

### Every Time You Push Code:
1. **GitHub creates a fresh Ubuntu virtual machine**
2. **Installs 4 different Python versions** (3.8, 3.9, 3.10, 3.11)
3. **Downloads your code**
4. **Installs pytest and your dependencies**
5. **Runs all your tests**
6. **Shows you ✅ or ❌ results**
7. **Generates coverage reports**

### Benefits:
- ✅ **Catch bugs early** - before they reach production
- ✅ **Test multiple Python versions** - ensure compatibility
- ✅ **Automatic testing** - no manual work needed
- ✅ **Team collaboration** - everyone sees test results
- ✅ **Prevents broken deployments** - only deploy working code

## Next Steps

Once your basic pipeline is working, you can add:
- **Deployment** - automatically deploy when tests pass
- **Notifications** - get alerts when builds fail
- **Code coverage badges** - show test coverage in your README
- **Security scanning** - check for vulnerabilities
- **Performance testing** - ensure code is fast enough

## Quick Reference

### File Structure:
```
your-repo/
├── .github/
│   └── workflows/
│       └── ci.yml
├── my_functions.py
├── test_my_function.py
├── requirements.txt
└── README.md
```

### Essential Commands:
```bash
# If you make changes locally and need to sync
git pull origin main
git push origin main

# Check pipeline status
# Go to repository → Actions tab
```

**🎉 Congratulations! You now have a professional CI/CD pipeline that automatically tests your code on every change!**