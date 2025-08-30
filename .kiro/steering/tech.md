# Technology Stack

## Content Format
- **Primary Language**: Japanese (日本語)
- **Content Format**: Markdown (.md files)
- **Structure**: Date-prefixed folders with descriptive names

## Development Environment
- **Platform**: macOS (darwin)
- **Shell**: zsh
- **File Management**: Standard file system operations

## Content Organization
- Articles are stored in date-prefixed directories (YYYYMMDD format)
- Each article directory contains:
  - `article.md` - Main content file
  - Supporting images (PNG format) when needed
- No build system or compilation required - pure markdown content

## Common Operations
Since this is a content repository, typical operations include:

```bash
# Create new article directory
mkdir YYYYMMDD_article-topic-name

# Create article file
touch YYYYMMDD_article-topic-name/article.md

# List articles by date
ls -la | grep "^d.*202"

# Search content across articles
grep -r "search-term" */article.md
```

## Content Standards
- Use Japanese for primary content
- Include code examples with proper syntax highlighting
- Add images to support technical explanations
- Structure articles with clear headings and table of contents
- Include practical examples and real-world use cases