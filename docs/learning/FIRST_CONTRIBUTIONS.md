# First Contributions

## 🎯 Purpose

This guide walks you through making your **first contribution** to Dify, from finding an issue to getting your PR merged.

**Time to complete:** 2-4 hours for your first PR

---

## 🚀 Quick Path to First PR

```
1. Find an issue (15 min)
   ↓
2. Set up locally (30 min)
   ↓
3. Create a branch (2 min)
   ↓
4. Make changes (30-60 min)
   ↓
5. Test locally (15 min)
   ↓
6. Commit & push (5 min)
   ↓
7. Create PR (10 min)
   ↓
8. Address review feedback (varies)
   ↓
9. Get merged! 🎉
```

---

## 1️⃣ Find a Good First Issue

### Browse Good First Issues

Visit: [Good First Issues](https://github.com/langgenius/dify/issues?q=is%3Aissue+state%3Aopen+label%3A%22good+first+issue%22)

**What to look for:**
- ✅ Labeled "good first issue"
- ✅ No one assigned yet
- ✅ Clear description
- ✅ Matches your skills (frontend/backend/docs)

### Issue Categories

**Frontend Issues:**
- UI bugs
- Component improvements
- Accessibility fixes
- Styling tweaks

**Backend Issues:**
- API bugs
- Database query optimizations
- Error handling
- Logging improvements

**Documentation Issues:**
- Typo fixes
- Documentation gaps
- Example code
- Translations

### Example Good First Issues

1. **"Add loading state to button component"** ⭐ Frontend
   - Files: `web/app/components/base/button/`
   - Difficulty: Easy
   - Time: 30 min

2. **"Fix typo in API error message"** ⭐ Backend
   - Files: `api/controllers/`
   - Difficulty: Easy
   - Time: 15 min

3. **"Add type hints to utility functions"** ⭐ Backend
   - Files: `api/libs/`
   - Difficulty: Medium
   - Time: 1 hour

---

## 2️⃣ Set Up Locally

If you haven't already, follow the **[Getting Started Guide](./GETTING_STARTED.md)**.

Quick checklist:
- [ ] Docker running
- [ ] Backend starts: `cd api && uv run --dev flask run`
- [ ] Frontend starts: `cd web && pnpm dev`
- [ ] You can access http://localhost:3000

---

## 3️⃣ Create Your Branch

### Fork the Repository

1. Go to [github.com/langgenius/dify](https://github.com/langgenius/dify)
2. Click "Fork" button
3. Clone YOUR fork:

```bash
git clone https://github.com/YOUR_USERNAME/dify.git
cd dify
```

### Add Upstream Remote

```bash
# Add the original repo as upstream
git remote add upstream https://github.com/langgenius/dify.git

# Verify
git remote -v
# origin    https://github.com/YOUR_USERNAME/dify.git (fetch)
# origin    https://github.com/YOUR_USERNAME/dify.git (push)
# upstream  https://github.com/langgenius/dify.git (fetch)
# upstream  https://github.com/langgenius/dify.git (push)
```

### Create Feature Branch

```bash
# Update main
git checkout main
git pull upstream main

# Create feature branch
git checkout -b fix/add-loading-state-to-button

# Branch naming:
# - fix/description - Bug fixes
# - feat/description - New features
# - docs/description - Documentation
# - refactor/description - Code refactoring
```

---

## 4️⃣ Make Your Changes

### Example: Fix a Frontend Bug

**Issue:** "Add loading state to button component"

```typescript
// web/app/components/base/button/index.tsx

// Before
export const Button = ({ children, onClick }) => {
  return <button onClick={onClick}>{children}</button>
}

// After
export const Button = ({
  children,
  onClick,
  loading = false  // Add loading prop
}) => {
  return (
    <button
      onClick={onClick}
      disabled={loading}  // Disable when loading
      className={loading ? 'opacity-50 cursor-not-allowed' : ''}
    >
      {loading ? (
        <>
          <LoadingSpinner className="mr-2" />
          {children}
        </>
      ) : (
        children
      )}
    </button>
  )
}
```

### Example: Fix a Backend Bug

**Issue:** "Add type hints to get_apps function"

```python
# api/services/app_service.py

# Before
def get_apps(tenant_id):
    return App.query.filter_by(tenant_id=tenant_id).all()

# After
def get_apps(tenant_id: str) -> list[App]:
    return App.query.filter_by(tenant_id=tenant_id).all()
```

---

## 5️⃣ Test Your Changes

### Frontend Testing

```bash
cd web

# Run linter
pnpm lint

# Fix linting issues
pnpm lint:fix

# Run type checker
pnpm type-check

# Run tests (if you added any)
pnpm test
```

### Backend Testing

```bash
cd api

# Run linter
make lint

# Run type checker
make type-check

# Run unit tests
uv run --project api --dev dev/pytest/pytest_unit_tests.sh
```

### Manual Testing

1. **Test the UI:**
   - Open http://localhost:3000
   - Navigate to the changed component
   - Verify it works as expected
   - Test edge cases

2. **Test the API:**
   ```bash
   # Example: Test the endpoint
   curl -X GET 'http://localhost:5001/console/api/apps' \
     -H 'Cookie: access_token=YOUR_TOKEN'
   ```

---

## 6️⃣ Commit and Push

### Write a Good Commit Message

Follow [Conventional Commits](https://www.conventionalcommits.org/):

```bash
# Format:
<type>(<scope>): <description>

[optional body]

[optional footer]
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation only
- `style`: Formatting, no code change
- `refactor`: Code change that neither fixes a bug nor adds a feature
- `test`: Adding tests
- `chore`: Maintenance tasks

**Examples:**

```bash
# Good ✅
git commit -m "feat(button): add loading state with spinner

- Add loading prop to Button component
- Disable button when loading
- Show spinner icon during loading state
- Add TypeScript types for new prop

Fixes #12345"

# Good ✅
git commit -m "fix(api): add type hints to get_apps function

Improves type safety and IDE autocomplete.

Fixes #67890"

# Bad ❌
git commit -m "fixed stuff"

# Bad ❌
git commit -m "updated button"
```

### Commit and Push

```bash
# Stage changes
git add .

# Commit
git commit -m "feat(button): add loading state with spinner

- Add loading prop to Button component
- Disable button when loading
- Show spinner icon during loading state

Fixes #12345"

# Push to YOUR fork
git push origin fix/add-loading-state-to-button
```

---

## 7️⃣ Create Pull Request

### On GitHub

1. Go to YOUR fork: `https://github.com/YOUR_USERNAME/dify`
2. Click "Compare & pull request" button
3. Fill out the PR template

### PR Template

```markdown
## Description

Added loading state to Button component with spinner icon.

## Type of Change

- [x] Bug fix (non-breaking change)
- [ ] New feature (non-breaking change)
- [ ] Breaking change (fix or feature that would cause existing functionality to not work as expected)
- [ ] Documentation update

## Testing

- [x] Tested locally
- [x] Added/updated tests
- [x] Linting passes (`pnpm lint`)
- [x] Type checking passes (`pnpm type-check`)

## Screenshots (if applicable)

Before:
[screenshot]

After:
[screenshot]

## Checklist

- [x] My code follows the code style of this project
- [x] I have added tests that prove my fix is effective or that my feature works
- [x] New and existing unit tests pass locally
- [x] I have commented my code in hard-to-understand areas
- [x] My changes generate no new warnings
- [x] I have updated the documentation accordingly
- [x] I have read the **CONTRIBUTING** document

## Related Issue

Fixes #12345
```

### Tips for Good PRs

✅ **DO:**
- Link the issue (`Fixes #123`)
- Add screenshots for UI changes
- Explain WHY, not just WHAT
- Keep changes focused (one issue per PR)
- Add tests if applicable

❌ **DON'T:**
- Submit huge PRs (>500 lines)
- Mix multiple unrelated changes
- Skip testing
- Leave debugging code (`console.log`, `print()`)

---

## 8️⃣ Code Review Process

### What to Expect

1. **Automated checks:** CI/CD runs tests
2. **Maintainer review:** Usually within 1-3 days
3. **Feedback:** Requested changes or approval
4. **Revisions:** You may need to update your PR

### Responding to Feedback

```bash
# Make requested changes
# ... edit files ...

# Commit changes
git add .
git commit -m "refactor: address review feedback

- Extract spinner to separate component
- Add prop validation
- Update tests"

# Push (will update existing PR)
git push origin fix/add-loading-state-to-button
```

### Common Review Feedback

**"Add types"**
```typescript
// ❌ Before
const handleClick = (data) => { }

// ✅ After
const handleClick = (data: ButtonData) => { }
```

**"Extract to constant"**
```typescript
// ❌ Before
if (status === 'active' || status === 'pending') { }

// ✅ After
const ACTIVE_STATUSES = ['active', 'pending']
if (ACTIVE_STATUSES.includes(status)) { }
```

**"Add error handling"**
```python
# ❌ Before
def get_app(app_id: str):
    return App.query.get(app_id)

# ✅ After
def get_app(app_id: str):
    app = App.query.get(app_id)
    if not app:
        raise AppNotFoundError(f"App {app_id} not found")
    return app
```

---

## 9️⃣ Get Merged! 🎉

Once approved, a maintainer will merge your PR.

**Congratulations!** You're now a Dify contributor!

### After Merge

1. **Update your fork:**
   ```bash
   git checkout main
   git pull upstream main
   git push origin main
   ```

2. **Delete your feature branch:**
   ```bash
   git branch -d fix/add-loading-state-to-button
   git push origin --delete fix/add-loading-state-to-button
   ```

3. **Find your next issue!** 🚀

---

## 🎁 Bonus: Advanced Contributions

### Larger Features

Once you're comfortable:
1. Browse issues without "good first issue" label
2. Propose new features in Discussions
3. Review other PRs
4. Help answer questions in Discord

### Becoming a Regular Contributor

**Regular contributors can:**
- Get assigned to issues
- Help triage new issues
- Review PRs from others
- Shape the project roadmap

---

## 🐛 Troubleshooting

### PR Conflicts

```bash
# Update your branch with latest main
git checkout main
git pull upstream main
git checkout your-feature-branch
git rebase main

# Resolve conflicts
# ... edit files ...
git add .
git rebase --continue

# Force push (rewrites history)
git push origin your-feature-branch --force
```

### CI Failing

1. **Check logs:** Click "Details" next to failed check
2. **Common issues:**
   - Linting errors: Run `pnpm lint:fix` or `make lint`
   - Test failures: Run tests locally
   - Type errors: Run `pnpm type-check` or `make type-check`
3. **Fix and push:** CI will re-run automatically

### Need Help?

- 💬 Ask in [Discord #engineering-help](https://discord.gg/FngNHpbcY7)
- 💬 Comment on your PR
- 💬 Ask in [GitHub Discussions](https://github.com/langgenius/dify/discussions)

---

## ✅ Checklist for Success

Before submitting your PR:

- [ ] Issue exists and is assigned to you
- [ ] Code works locally
- [ ] Tests pass (`pnpm test` or `pytest`)
- [ ] Linting passes (`pnpm lint` / `make lint`)
- [ ] Type checking passes (`pnpm type-check` / `make type-check`)
- [ ] Commit message follows convention
- [ ] PR description is complete
- [ ] Screenshots added (if UI change)
- [ ] Documentation updated (if needed)
- [ ] No debugging code left (`console.log`, `print()`)

---

## 📚 Next Steps

After your first PR:

1. **[How-To Guide](./HOW_TO_GUIDE.md)** - Build more complex features
2. **[Code Tours](./CODE_TOURS.md)** - Understand larger systems
3. **[Testing Guide](./TESTING_GUIDE.md)** - Write better tests
4. **[Patterns and Conventions](./PATTERNS_AND_CONVENTIONS.md)** - Write idiomatic code

---

## 🌟 Contributor Recognition

All contributors are recognized in:
- [Contributors page](https://github.com/langgenius/dify/graphs/contributors)
- README contributors section
- Release notes (for significant contributions)

**Thank you for contributing to Dify!** 🙏

---

*Every expert was once a beginner. Your first PR is a big step!* 🚀
