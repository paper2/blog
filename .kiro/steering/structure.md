# Project Structure

## Directory Organization

```
/
├── .git/                    # Git repository metadata
├── .kiro/                   # Kiro configuration and steering rules
│   └── steering/           # AI assistant steering documents
├── YYYYMMDD_topic-name/    # Article directories (date-prefixed)
│   ├── article.md          # Main article content
│   ├── image.png           # Supporting images
│   ├── image-1.png         # Additional numbered images
│   └── image-N.png         # More images as needed
```

## Naming Conventions

### Article Directories
- Format: `YYYYMMDD_descriptive-topic-name`
- Date format: 8-digit date (YYYYMMDD)
- Topic: Lowercase, hyphen-separated descriptive name
- Examples:
  - `20250224_github-actions-opentelemetry`
  - `20250322_github-actions-prod-only`
  - `20250531_unit-test-thinking-and-how-to-use`

### Files
- **Main content**: Always named `article.md`
- **Images**: 
  - Primary image: `image.png`
  - Additional images: `image-1.png`, `image-2.png`, etc.
  - Format: PNG preferred for technical diagrams and screenshots

## Content Structure Patterns

### Article Format
Each `article.md` follows these patterns:
- Japanese language content
- Technical focus with practical examples
- Code snippets with proper syntax highlighting
- Supporting images referenced in content
- Table of contents using `[:contents]` marker
- Clear section headings with `#` markdown syntax

### Common Article Types
1. **Tutorial/How-to**: Step-by-step technical guides
2. **Experience Reports**: Lessons learned from real projects
3. **Tool Reviews**: Analysis of development tools and practices
4. **Book Reviews**: Technical book summaries and insights

## File Management Guidelines
- One article per directory
- Keep related assets (images) in the same directory
- Use descriptive directory names that reflect article content
- Maintain chronological organization through date prefixes