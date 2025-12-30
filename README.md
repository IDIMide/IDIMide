# IDIMide Promotional Page

A modern, responsive promotional page for IDIMide - An Experimental Tracker.

## Features
- Sleek dark theme design
- Fully responsive layout
- Smooth animations and hover effects
- Optimized for GitHub Pages

## Deploying to GitHub Pages

### Option 1: Using GitHub Web Interface

1. Create a new repository on GitHub:
   - Go to https://github.com/new
   - Name it `idimide` (or any name you prefer)
   - Make it **Public**
   - Don't initialize with README (we already have one)
   - Click "Create repository"

2. Push your code:
   ```bash
   git remote add origin https://github.com/YOUR_USERNAME/idimide.git
   git branch -M main
   git push -u origin main
   ```

3. Enable GitHub Pages:
   - Go to your repository settings
   - Navigate to "Pages" in the left sidebar
   - Under "Source", select "Deploy from a branch"
   - Select branch: `main` and folder: `/ (root)`
   - Click "Save"

4. Your site will be live at: `https://YOUR_USERNAME.github.io/idimide/`

### Option 2: Using GitHub CLI (gh)

If you have GitHub CLI installed:

```bash
gh repo create idimide --public --source=. --remote=origin --push
gh browse --settings
```

Then enable Pages in the settings as described above.

## Local Development

Simply open `index.html` in your browser to preview the page locally.

## Credits

Design and development for IDIMide tracker.
