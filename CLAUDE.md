# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **dual-brand Jekyll static website** serving both:
- **WelcomeSecurity**: Security consulting (Security Sidekick for developers)
- **Sustira**: ESG and sustainability consulting

Both brands share the same codebase and are toggled through content and configuration. The site is built as static HTML and deployed to external hosting.

## Build and Development Commands

### Local Development
```bash
bundle install                    # Install dependencies
bundle exec jekyll serve          # Run dev server at http://localhost:4000
bundle exec jekyll build          # Build to output/ directory
```

### Testing
- No automated tests configured
- Manual testing via local server
- GitHub Actions runs link checking and health checks

## Architecture

### Content Structure
- **Collections** (`_services/`, `_team/`): Markdown files with frontmatter for services and team members
- **Data files** (`_data/`): Structured YAML data for menus, SEO, contact info
- **Pages**: Root-level `.md` files (index.md, about.md, contact.md, etc.)

### Key Configuration Files
- `_config.yml`: Main Jekyll config (site title, URL, collections, build settings)
- `_data/seo.yml`: Copyright text, meta descriptions, keywords
- `_data/menus.yml`: Main navigation and footer menu structure
- `_data/contact.yml`: Contact information

### Layout Hierarchy
- `_layouts/default.html`: Base template with Schema.org structured data, meta tags, header/footer
- Specialized layouts: `home.html`, `service.html`, `team.html`, `page.html`, `contact.html`
- Includes: `_includes/header.html`, `_includes/footer.html`, `_includes/sub-footer.html`

### Styling
- SASS files in `_sass/`
- Custom overrides in `_sass/components/_welcomesecurity-custom.scss`
- CSS is purged during deployment using PurgeCSS (preserves: 'open', 'is-active', 'lock-scroll', 'header-shrunk', 'header-shrunk-body')

### JavaScript
- `assets/js/scripts.js`: Shrinking header on scroll, mobile menu

## Deployment

### Automated Deployment (GitHub Actions)
- **Trigger**: Push to `master` branch
- **Process**:
  1. Build Jekyll site (`JEKYLL_ENV=production`)
  2. Purge unused CSS
  3. Upload to server via SCP (password auth)
  4. Atomic swap using rename (htdocs backed up to `.backup_*`)
  5. Rollback on failure
- **Config**: Environment variables in GitHub (DEPLOY_HOST, DEPLOY_USER, DEPLOY_PORT, DEPLOY_PATH)
- **Secret**: DEPLOY_SSH_PASSWORD

### Build Output
- Directory: `output/` (never edit manually)
- Includes dotfiles (`.htaccess`, `.well-known/`)

## Health Monitoring

Automated daily checks (03:00 UTC):
- Site availability and response time
- SSL certificate expiry (warns <30 days)
- Broken links (internal and external)

## Important Patterns

### Dual-Brand Content
Content references both WelcomeSecurity and Sustira. When editing:
- Check `_data/seo.yml` for copyright text
- Check `_layouts/default.html` for Schema.org structured data
- Service descriptions live in `_services/`
- Team bios reference both brands in `_team/`

### URL Structure
- Base URL: `https://www.sustira.dk` (configured in _config.yml)
- Empty baseurl (deployed to site root)
- Pretty permalinks (no .html extensions)

### Security Headers
- `.htaccess`: Security headers configuration
- `.well-known/security.txt`: Security contact and policy

### Static Site Benefits
- No WordPress, PHP, or database
- Minimal attack surface
- Fast, cacheable content
- Version-controlled content

## Branch Strategy
- **master**: Production branch (auto-deploys)
- **gh-pages**: Current working branch (based on git status provided)

## Common Tasks

### Adding a New Service
1. Create markdown file in `_services/`
2. Add frontmatter (title, description, weight for ordering)
3. Service will auto-appear on services page

### Updating Team Members
1. Edit markdown in `_team/`
2. Frontmatter includes: name, jobtitle, email, image, description

### Changing Navigation
1. Edit `_data/menus.yml`
2. Separate `main:` and `footer:` menu arrays
3. Weight determines ordering

### Updating Footer Copyright
1. Edit `_data/seo.yml`
2. Look for `copyright_text:` field

### Schema.org Structured Data
- Located in `_layouts/default.html` (lines 23-80)
- Type: ProfessionalService
- Update when business info changes (address, phone, services)
