# Husky Commit Validation -- Maven Project

This project uses **Husky** to enforce:

1.  ✅ Run Maven tests before commit (`pre-commit`)
2.  ✅ Enforce commit message format (`commit-msg`)

------------------------------------------------------------------------

# 📦 Requirements

-   Node.js (LTS recommended)
-   npm
-   Git (Git for Windows includes Git Bash)
-   Maven

Check versions:

``` bash
node -v
npm -v
git --version
mvn -v
```

------------------------------------------------------------------------

# 🚀 Installation & Setup

## 1️⃣ Initialize npm in Maven project

``` bash
npm init -y
```

## 2️⃣ Install Husky

``` bash
npm install husky --save-dev
```

## 3️⃣ Enable Husky

Add this to `package.json`:

``` json
{
  "scripts": {
    "prepare": "husky"
  }
}
```

Then run:

``` bash
npm run prepare
```

Verify:

``` bash
git config core.hooksPath
```

Expected:

    .husky

If you see:

    .husky/_

Fix it:

``` bash
git config core.hooksPath .husky
```

------------------------------------------------------------------------

# 📂 Expected Folder Structure

    project-root/
     ├── pom.xml
     ├── package.json
     ├── node_modules/
     └── .husky/
          ├── _/
          │    └── husky.sh
          ├── pre-commit
          └── commit-msg

⚠️ Hooks must NOT be inside `.husky/_`

------------------------------------------------------------------------

# 🧪 Pre-Commit Hook (Run Maven Tests)

Create:

`.husky/pre-commit`

``` sh
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

echo "Running Maven validation..."
mvn test
```

Make executable:

``` bash
chmod +x .husky/pre-commit
```

If tests fail → commit is blocked.

------------------------------------------------------------------------

# 📝 Commit Message Validation

Required format:

    feat: description [TASK-1234]

Allowed types:

-   feat
-   fix
-   chore
-   docs
-   refactor
-   test
-   perf

Create:

`.husky/commit-msg`

``` sh
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

commit_msg_file="$1"
commit_msg="$(cat "$commit_msg_file")"

pattern='^(feat|fix|chore|docs|refactor|test|perf): .+ \[TASK-[0-9]+\]$'

echo "$commit_msg" | grep -Eq "$pattern"
if [ $? -ne 0 ]; then
  echo "❌ Invalid commit message format!"
  echo ""
  echo "Required format:"
  echo "feat: description [TASK-1234]"
  echo ""
  echo "Example:"
  echo "feat: add login validation [TASK-1000]"
  exit 1
fi
```

Make executable:

``` bash
chmod +x .husky/commit-msg
```

------------------------------------------------------------------------

# ✅ Valid Commit Example

``` bash
git commit -m "feat: add login validation [TASK-1000]"
```

------------------------------------------------------------------------

# ❌ Invalid Commit Example

``` bash
git commit -m "added stuff"
```

Commit will be blocked.

------------------------------------------------------------------------

# 💻 Windows / PowerShell Notes

You can commit from PowerShell:

``` powershell
git commit -m "feat: add login validation [TASK-1000]"
```

Hooks run using Git's internal shell (Git Bash), not PowerShell.

------------------------------------------------------------------------

# 🛑 Skip Hooks (Temporary)

``` bash
git commit -m "message" --no-verify
```

------------------------------------------------------------------------

# 🧯 Disable Husky Temporarily

``` bash
export HUSKY=0
```

Re-enable:

``` bash
unset HUSKY
```

------------------------------------------------------------------------

# ❌ Completely Remove Husky

``` bash
rm -rf .husky
npm uninstall husky
rm package.json package-lock.json
rm -rf node_modules
git config --unset core.hooksPath
```

------------------------------------------------------------------------

# 🧠 How It Works

When you run:

``` bash
git commit
```

Git:

1.  Reads `core.hooksPath`
2.  Executes `.husky/pre-commit`
3.  Executes `.husky/commit-msg`
4.  If any script returns `exit 1` → commit fails

Husky simply manages Git hook scripts inside `.husky`.

------------------------------------------------------------------------

# 🎯 Summary

This setup ensures:

-   ✅ No broken tests are committed
-   ✅ All commits follow format:\
    `feat: description [TASK-1234]`
