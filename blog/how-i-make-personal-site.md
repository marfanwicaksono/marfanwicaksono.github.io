---
title: "How I Make Personal Site"
slug: "how-i-make-personal-site"
date: "2024-02-25"
excerpt: "A deep dive into how I built my personal portfolio website using React, Vite, TypeScript, and Tailwind CSS. Learn about the architecture, design decisions, and deployment strategies."
tags: ["web-development", "react", "vite", "portfolio", "tutorial"]
author: "Mokhamad Arfan Wicaksono"
readingTime: 10
published: true
---

# How I Make Personal Site

Building a personal portfolio website is a journey that combines creativity, technical skills, and strategic thinking. In this article, I'll walk you through how I built my personal site from scratch, covering the technology stack, architecture, design decisions, and deployment strategies.

## Why Build Your Own Site?

Before diving into the technical details, let's talk about why you might want to build your own portfolio website:

- **Full Control**: Complete freedom over design, content, and functionality
- **Learning Experience**: Opportunity to work with modern web technologies
- **SEO Benefits**: Better control over your online presence
- **Flexibility**: Easy to update and extend as your career grows
- **Performance**: Optimize for speed instead of using heavy templates

## Technology Stack

I chose a modern, performance-focused stack for my portfolio:

### Core Technologies
- **React 18.3** - Component-based UI library
- **TypeScript 5.6** - Type-safe JavaScript
- **Vite 6.0** - Lightning-fast build tool and dev server
- **Tailwind CSS 3.4** - Utility-first CSS framework

### Additional Libraries
- **React Router DOM 6.28** - Client-side routing
- **Framer Motion 11.15** - Smooth animations
- **React Markdown 9.0** - Markdown rendering for blog
- **React Helmet Async** - SEO meta tags management
- **Lucide React** - Beautiful icons
- **Zod** - Schema validation for forms

### Why This Stack?

**Vite over CRA**: Vite offers significantly faster build times and hot module replacement (HMR) compared to Create React App. It uses native ES modules in development and Rollup for production builds.

**TypeScript**: Catch errors at compile time, better IDE support, and self-documenting code. Essential for maintaining a larger codebase.

**Tailwind CSS**: Rapid UI development without writing custom CSS. The utility-first approach keeps styles consistent and maintainable.

**Framer Motion**: Declarative animations that integrate seamlessly with React. Much easier than CSS animations for complex interactions.

## Project Architecture

The project follows a clean, modular structure:

```
personal-site/
├── public/
│   ├── data/              # JSON data files
│   │   ├── personal.json
│   │   ├── experience.json
│   │   └── ... other sections
│   ├── blog/              # Markdown blog posts
│   └── images/            # Static assets
├── src/
│   ├── components/
│   │   ├── common/        # Reusable components
│   │   ├── layout/        # Header, Footer, Layout
│   │   ├── sections/      # Hero, About, Blog, etc.
│   │   └── theme/         # Theme toggle
│   ├── context/           # React Context providers
│   ├── hooks/             # Custom hooks
│   ├── pages/             # Page components
│   └── utils/             # Helper functions
└── scripts/
    └── generate-resume-pdf.mjs
```

### Key Design Decisions

**JSON-Driven Content**: All portfolio data (experience, education, skills, projects) is stored in JSON files in the `public/data/` directory. This makes it incredibly easy to update content without touching React components.

**Markdown Blog**: Blog posts are written in Markdown with frontmatter metadata. This allows for easy writing and version control of articles.

**Component Organization**: Components are organized by function (common, layout, sections, theme) rather than by type, making the codebase easier to navigate.

## Core Features

### 1. Dark Mode Support

The site supports light and dark themes with smooth transitions. Implemented using:

- **React Context**: `ThemeContext` manages theme state globally
- **Local Storage**: Persists user preference across sessions
- **Tailwind**: Dark mode variants (`dark:bg-gray-900`)
- **System Preference**: Respects user's OS preference by default

```typescript
// ThemeContext.tsx
const ThemeContext = createContext<ThemeContextType>({
  theme: 'light',
  toggleTheme: () => {},
});

export const ThemeProvider: React.FC<{ children: React.ReactNode }> = ({ children }) => {
  const [theme, setTheme] = useState<Theme>('light');

  useEffect(() => {
    const savedTheme = localStorage.getItem('theme') as Theme;
    const systemTheme = window.matchMedia('(prefers-color-scheme: dark)').matches ? 'dark' : 'light';
    const initialTheme = savedTheme || systemTheme;
    setTheme(initialTheme);
    document.documentElement.classList.toggle('dark', initialTheme === 'dark');
  }, []);

  const toggleTheme = () => {
    const newTheme = theme === 'light' ? 'dark' : 'light';
    setTheme(newTheme);
    localStorage.setItem('theme', newTheme);
    document.documentElement.classList.toggle('dark', newTheme === 'dark');
  };

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};
```

### 2. Responsive Design

The site is fully responsive using Tailwind's responsive utilities:

- **Mobile-first approach**: Start with mobile styles, add breakpoints for larger screens
- **Breakpoints**: `sm:`, `md:`, `lg:`, `xl:` for different screen sizes
- **Flexible layouts**: Grid and Flexbox for adaptive layouts
- **Touch-friendly**: Appropriate tap target sizes (minimum 44x44px)

### 3. Smooth Animations

Animations enhance the user experience without being distracting:

```typescript
// Fade in animation with Framer Motion
<motion.div
  initial={{ opacity: 0, y: 20 }}
  animate={{ opacity: 1, y: 0 }}
  transition={{ duration: 0.5 }}
>
  {/* Content */}
</motion.div>
```

Custom animations in `tailwind.config.ts`:

```typescript
animation: {
  'fade-in': 'fadeIn 0.5s ease-in',
  'slide-up': 'slideUp 0.5s ease-out',
  'bounce-slow': 'bounce 3s infinite',
},
keyframes: {
  fadeIn: {
    '0%': { opacity: '0' },
    '100%': { opacity: '1' },
  },
  slideUp: {
    '0%': { transform: 'translateY(20px)', opacity: '0' },
    '100%': { transform: 'translateY(0)', opacity: '1' },
  },
},
```

### 4. SEO Optimization

The site includes comprehensive SEO features:

- **Meta Tags**: Title, description, keywords for each page
- **Open Graph**: Social media sharing previews
- **Twitter Cards**: Enhanced Twitter sharing
- **JSON-LD**: Structured data for search engines
- **Sitemap**: Automatic sitemap generation
- **Robots.txt**: Search engine crawling instructions

```typescript
// SEO component
export const SEO: React.FC<SEOProps> = ({
  title,
  description,
  keywords,
  ogImage,
  ogType = 'website',
}) => {
  const { site } = useSiteConfig();

  return (
    <Helmet>
      <title>{title} | {site.name}</title>
      <meta name="description" content={description} />
      <meta name="keywords" content={keywords.join(', ')} />

      {/* Open Graph */}
      <meta property="og:title" content={title} />
      <meta property="og:description" content={description} />
      <meta property="og:image" content={ogImage || site.ogImage} />
      <meta property="og:type" content={ogType} />

      {/* Twitter Card */}
      <meta name="twitter:card" content="summary_large_image" />
      <meta name="twitter:title" content={title} />
      <meta name="twitter:description" content={description} />
      <meta name="twitter:image" content={ogImage || site.ogImage} />
    </Helmet>
  );
};
```

### 5. Blog System

The blog is built with simplicity in mind:

- **Markdown Posts**: Write posts in Markdown with frontmatter
- **Syntax Highlighting**: Code blocks with `rehype-highlight`
- **GitHub Flavored Markdown**: Tables, task lists, strikethrough
- **Tag Filtering**: Filter posts by tags
- **Reading Time**: Automatic calculation based on word count

```typescript
// Blog post frontmatter example
---
title: "How I Make Personal Site"
slug: "how-i-make-personal-site"
date: "2024-02-25"
excerpt: "A deep dive into building a personal portfolio..."
tags: ["web-development", "react", "vite"]
author: "Mokhamad Arfan Wicaksono"
readingTime: 10
published: true
---
```

## Performance Optimization

Performance is a key focus. Here's how the site stays fast:

### Code Splitting

Vite automatically splits code, but I've configured manual chunks for better caching:

```typescript
// vite.config.ts
export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        manualChunks: {
          'react-vendor': ['react', 'react-dom', 'react-router-dom'],
          'motion': ['framer-motion'],
          'markdown': ['react-markdown', 'remark-gfm'],
        },
      },
    },
  },
});
```

### Lazy Loading

Components and images are loaded only when needed:

```typescript
// Lazy load blog post page
const BlogPostPage = lazy(() => import('./pages/BlogPostPage'));

// In router
<Suspense fallback={<LoadingSpinner />}>
  <Route path="/blog/:slug" element={<BlogPostPage />} />
</Suspense>
```

### Image Optimization

- **WebP Format**: Use modern image formats when possible
- **Responsive Images**: Serve different sizes for different devices
- **Lazy Loading**: Load images as they enter the viewport
- **Compression**: Optimize images before adding to the project

### Build Output

Typical production build sizes:
- **HTML**: ~0.7 KB (gzipped)
- **CSS**: ~7.8 KB (gzipped)
- **JavaScript**: ~200 KB total (gzipped, split across chunks)

## Content Management

Managing content is designed to be simple and developer-friendly:

### JSON Data Files

All portfolio content is in JSON files:

```json
// public/data/experience.json
[
  {
    "id": "1",
    "company": "Tech Company",
    "position": "Senior Developer",
    "startDate": "2022-01",
    "endDate": null,
    "description": "Building awesome things...",
    "technologies": ["React", "TypeScript", "Node.js"],
    "visible": true
  }
]
```

### Adding New Content

1. **Blog Post**: Create `.md` file in `public/blog/` and add to `index.json`
2. **Project**: Add entry to `public/data/projects.json`
3. **Experience**: Add entry to `public/data/experience.json`
4. **Skills**: Update `public/data/skills.json`

No need to touch React components!

## Deployment

The site supports dual deployment:

### Cloudflare Pages

**Why Cloudflare Pages?**
- Global CDN for fast delivery
- Automatic HTTPS
- Preview deployments for every commit
- Easy deployment with Wrangler CLI

**Deployment Process**:

```bash
# Build and deploy to preview
npm run deploy

# Build and deploy to production
npm run deploy:prod
```

Configuration in `wrangler.toml`:

```toml
name = "marfanwicaksono"
compatibility_date = "2025-01-01"
pages_build_output_dir = "dist"
```

### GitHub Pages

Alternative deployment using GitHub Pages:

```bash
# Build the project
npm run build

# The dist/ folder is a git submodule
cd dist
git add -A
git commit -m "deploy"
git push
```

**SPA Routing**: GitHub Pages doesn't support SPA routing out of the box. I implemented a clever workaround using `public/404.html` that:
1. Stores the requested path in `sessionStorage`
2. Redirects to `/`
3. `index.html` restores the path using `history.replaceState()`

## Development Workflow

### Getting Started

```bash
# Clone repository
git clone https://github.com/marfanwicaksono/personal-site.git
cd personal-site

# Install dependencies
npm install

# Start dev server
npm run dev
```

### Available Scripts

| Command | Description |
|---------|-------------|
| `npm run dev` | Start development server (http://localhost:5173) |
| `npm run build` | Build for production |
| `npm run preview` | Preview production build locally |
| `npm run lint` | Run ESLint |
| `npm run generate:resume` | Generate resume PDFs |
| `npm run deploy` | Deploy to Cloudflare Pages (preview) |
| `npm run deploy:prod` | Deploy to Cloudflare Pages (production) |

## Customization Tips

### Changing Colors

Edit `tailwind.config.ts`:

```typescript
colors: {
  primary: {
    50: '#f0f9ff',
    100: '#e0f2fe',
    // ... up to 950
  }
}
```

### Adding Sections

1. Create JSON data file in `public/data/`
2. Create section component in `src/components/sections/`
3. Add section to `src/pages/Home.tsx`
4. Add data fetching hook in `src/hooks/`

### Custom Animations

Add to `tailwind.config.ts`:

```typescript
animation: {
  'custom-animation': 'customKeyframe 1s ease-in-out',
},
keyframes: {
  customKeyframe: {
    '0%': { transform: 'scale(1)' },
    '50%': { transform: 'scale(1.1)' },
    '100%': { transform: 'scale(1)' },
  },
},
```

## Lessons Learned

### What Worked Well

1. **JSON-driven content**: Made updates incredibly simple
2. **Vite**: Fast builds improved development experience significantly
3. **Tailwind**: Rapid UI development without custom CSS
4. **TypeScript**: Caught many bugs before runtime
5. **Component architecture**: Easy to maintain and extend

### Challenges Faced

1. **SPA routing on GitHub Pages**: Required creative workaround
2. **Dark mode implementation**: Needed careful handling of system preferences
3. **Performance optimization**: Required careful code splitting
4. **SEO for SPA**: Needed proper meta tag management

### What I'd Do Differently

1. **Start with Tailwind**: Would have saved time vs. custom CSS
2. **Use content management**: Consider a headless CMS for frequent updates
3. **Add testing**: Implement tests for critical components
4. **Monitor analytics**: Add analytics from the start

## Future Improvements

Here are some features I'm considering:

- **Search Functionality**: Full-text search for blog posts
- **Newsletter**: Email subscription for new posts
- **Comments**: Commenting system for blog posts
- **Analytics Dashboard**: View site traffic and engagement
- **Dark Mode Images**: Optimized images for dark mode
- **PWA Support**: Offline functionality and installability
- **i18n**: Multi-language support
- **RSS Feed**: RSS/Atom feed for blog posts

## Conclusion

Building my personal site was a rewarding experience that allowed me to work with modern web technologies and create something that truly represents me. The combination of React, Vite, TypeScript, and Tailwind CSS provided an excellent developer experience and resulted in a fast, maintainable website.

The JSON-driven content approach makes it easy to keep the site updated, while the modular architecture allows for easy extension and customization. Whether you're a developer, designer, or creative professional, building your own portfolio site is a worthwhile investment in your personal brand.

## Resources

- **Live Site**: [marfanwicaksono.pages.dev](https://marfanwicaksono.pages.dev)
- **Source Code**: [github.com/marfanwicaksono/personal-site](https://github.com/marfanwicaksono/personal-site)
- **Technologies Used**:
  - [React](https://react.dev/)
  - [Vite](https://vitejs.dev/)
  - [TypeScript](https://www.typescriptlang.org/)
  - [Tailwind CSS](https://tailwindcss.com/)
  - [Framer Motion](https://www.framer.com/motion/)
  - [Cloudflare Pages](https://pages.cloudflare.com/)

---

Happy building! If you have any questions or want to share your own portfolio site, feel free to reach out.
