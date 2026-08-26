<img width="768" height="637" alt="Screenshot 2026-08-26 211359" src="https://github.com/user-attachments/assets/29797738-0ef5-49aa-bb45-048cd7fa78c4" />
<img width="502" height="641" alt="Screenshot 2026-08-26 212024" src="https://github.com/user-attachments/assets/bd87b68e-7b84-4673-bd99-318758f10e87" />
<img width="870" height="634" alt="Screenshot 2026-08-26 212113" src="https://github.com/user-attachments/assets/40fc056e-3104-4513-84f2-c483bf1f3903" />
<img width="1152" height="655" alt="Screenshot 2026-08-26 212324" src="https://github.com/user-attachments/assets/52e063bd-5f65-4e6d-bfcf-92ea7b0e44fd" />


> **Rebase takes your feature branch commits and replays them on top of the latest `main`, giving a cleaner, more linear history.**

## 🔄 What happens step by step

Suppose initially:

```text
A ─── B                 main
     \
      C ─── D           feature-rebase-demo
```

* `A` = common starting point
* `B` = a newer commit on `main`
* `C`, `D` = commits made on the feature branch

Now you run:

```bash
git switch feature-rebase-demo
git rebase main
```

Git takes:

```text
C ─── D
```

temporarily, updates the feature branch to the latest `main`, and **replays C and D**.

The result becomes:

```text
A ─── B ─── C' ─── D'
                  ↑
          feature-rebase-demo
```

Notice `C'` and `D'` are technically **new commits** because rebase recreates them on the new base.

---

# 🎯 Your seminar demonstration

## 1. Start from `main`

```bash
git switch main
```

Make sure it's clean:

```bash
git status
```

You want:

```text
nothing to commit, working tree clean
```

---

## 2. Create the feature branch

```bash
git switch -c feature-rebase-demo
```

Explain:

> "I'm creating a separate feature branch so that I can demonstrate rebase safely without changing the shared main branch."

---

## 3. Make a feature commit

For example, modify `index.html`:

```html
<p>Feature A</p>
```

Then:

```bash
git add .
git commit -m "Add feature A"
```

Your history:

```text
A ─── B ─── C
          ↑
       feature
```

---

# 4. Go to `main`

```bash
git switch main
```

Now make a **different change** on main:

```html
<p>Main Update</p>
```

Then:

```bash
git add .
git commit -m "Update main"
```

Now the branches have diverged:

```text
          C ← feature-rebase-demo
         /
A ───── B
         \
          D ← main
```

This is the important setup.

---

# 5. Switch back to feature

```bash
git switch feature-rebase-demo
```

Check the graph:

```bash
git log --oneline --graph --all
```

You should see the branches have different histories.

---

# 6. Run `git rebase main` ⭐

Now:

```bash
git rebase main
```

Git takes the feature commit and replays it on top of the latest `main`.

Before:

```text
          C
         /
A ───── B
         \
          D ← main
```

After:

```text
A ───── B ───── D ───── C'
                         ↑
                 feature-rebase-demo
```

Now the history is **linear**.

---

# 🆚 Merge vs Rebase

This is the most important part for your seminar.

### 🔀 Merge

```bash
git switch main
git merge feature-rebase-demo
```

Can produce:

```text
      C
     / \
A ──B───M
     \ /
      D
```

The branch history is preserved, and a merge commit may be created.

### 🔄 Rebase

```bash
git switch feature-rebase-demo
git rebase main
```

Produces a cleaner history:

```text
A ─── B ─── D ─── C'
```

The feature commits are **replayed on top of `main`**.

---

# ⚠️ Why your notes say "controlled demonstration"

Rebase **rewrites commit history**.

For example:

```text
Original:

A ─── B ─── C


After rebase:

A ─── B ─── C'
```

`C'` contains the same logical work as `C`, but it is a **new commit with a different commit identity**.

Therefore, avoid rebasing branches that other people are already depending on.

### Good:

```text
feature-rebase-demo
        ↓
      rebase
        ↓
      main
```

### Avoid casually doing:

```text
shared feature branch
        ↓
      rebase
        ↓
other developers' work
```

---

> **"Merge combines two branch histories and may create a merge commit. Rebase instead takes my feature commits and replays them on top of the latest main branch. This creates a cleaner, linear history, but because rebase rewrites commit history, I should avoid rebasing shared branches."**

## ⭐ Your complete demo

```text
main
 ↓
Create feature branch
 ↓
Make feature commit
 ↓
Switch to main
 ↓
Make main commit
 ↓
Switch to feature
 ↓
git rebase main
 ↓
Feature commits replayed
 ↓
Clean linear history
```

And the key commands are just:

```bash
git switch -c feature-rebase-demo
git add .
git commit -m "Add feature"

git switch main
git add .
git commit -m "Update main"

git switch feature-rebase-demo
git rebase main
```


## resolve method:
Your important output is:

```text
CONFLICT (content): Merge conflict in index.html
error: could not apply 2d94fc3... rebase
```

This happened because:

```text
main                  feature-rebase-demo
 │                           │
 │                           └── 2d94fc3 "rebase"
 │
 └── 6ed4852 "Update main"
```

Both branches changed the same part of `index.html`, so Git needs you to decide what the final version should be.

---

# 🔄 You are currently inside a REBASE

**Do NOT run:**

```bash
git commit
```

during this step.

For rebase, the correct sequence is:

```text
Resolve conflict
      ↓
git add
      ↓
git rebase --continue
```

---

# 1. Check the status

Run:

```bash
git status
```

It should say something like:

```text
You are currently rebasing...
You have unmerged paths
    both modified: index.html
```

---

# 2. Open `index.html`

You'll probably see conflict markers like:

```html
<<<<<<< HEAD
<p>Update from main</p>
=======
<p>Feature rebase change</p>
>>>>>>> 2d94fc3 (rebase)
```

The meaning is:

```text
<<<<<<< HEAD
        ↓
Latest main version
```

and:

```text
=======
        ↓
Your feature commit being replayed
```

---

# 3. Resolve the conflict

For your seminar, I recommend keeping **both changes**.

For example:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Git Merge Demo</title>
</head>
<body>
    <h1>Git Merge Demo</h1>
    <p>Initial Project</p>
    <p>Update from main</p>
    <p>Feature rebase change</p>
</body>
</html>
```

The exact content depends on what you added, but the important thing is:

### Remove all these markers:

```text
<<<<<<<
=======
>>>>>>>
```

Save the file.

---

# 4. Stage the resolved file

Run:

```bash
git add index.html
```

Then:

```bash
git status
```

You'll likely see:

```text
rebase in progress
all conflicts fixed
```

---

# 5. Continue the rebase ⭐

Now run:

```bash
git rebase --continue
```

### If an editor opens

Git may ask for the commit message.

If Vim opens, use:

```text
Esc
:wq
Enter
```

You can keep the existing commit message:

```text
rebase
```

---

# 6. Check the result

After rebase completes:

```bash
git status
```

You should get:

```text
On branch feature-rebase-demo
nothing to commit, working tree clean
```

Then:

```bash
git log --oneline --graph --all
```

### Before rebase, you had:

```text
* 6ed4852 (main) Update main
| * 2d94fc3 (feature-rebase-demo) rebase
|/
* 34b5cfa instruction
```

### After successful rebase, you'll have something like:

```text
* XXXXXXX (HEAD -> feature-rebase-demo) rebase
* 6ed4852 (main) Update main
* 34b5cfa instruction
```

🎯 **That's the key point of your demo.**

The feature commit has been **replayed on top of `main`**.

---

# 🎤 What to say in your seminar

When you get this:

```text
CONFLICT (content): Merge conflict in index.html
```

say:

> **"During rebase, Git is replaying my feature commit on top of the latest main. Since both versions changed the same part of the file, Git cannot automatically resolve it, so I need to resolve the conflict manually."**

Then:

```bash
git add index.html
git rebase --continue
```

Say:

> **"After resolving the conflict, I stage the file and tell Git to continue the rebase."**

---

# 🔀 Your Merge vs Rebase demonstration

You've now got a very nice comparison.

### MERGE

You previously did:

```bash
git merge feature-contributor
```

Result:

```text
      feature
        ↓
A ─── B ─── M
     \     /
      C ──
```

Potentially creates a **merge commit**.

### REBASE

You're doing now:

```bash
git rebase main
```

Result:

```text
A ─── B ─── C'
          ↑
       feature
```

The feature commit is **replayed on top of main**, producing a linear history.

---

## ⚠️ Important

Because you're **currently in a rebase**, don't switch branches or run another merge.

Do these in order:

```bash
git status
```

Resolve `index.html`

```bash
git add index.html
git rebase --continue
```

Then:

```bash
git status
git log --oneline --graph --all
```

If `git rebase --continue` gives another error, **send me that exact output before running anything else**.
