---
title: "Building a Smart Invoice Generator with AI"
slug: "building-smart-invoice-generator-with-ai"
date: "2025-12-25"
excerpt: "How I built an AI-powered invoice generator that transforms natural language into professional invoices using React, Cloudflare Workers, and AI."
tags: ["react", "typescript", "cloudflare", "ai", "web-development"]
author: "Mokhamad Arfan Wicaksono"
readingTime: 8
published: true
---

# Building a Smart Invoice Generator with AI

Invoicing is one of those tedious tasks that every freelancer and business owner faces. Creating professional invoices manually is time-consuming and error-prone. So I decided to build something better: a **Smart Invoice Generator** that uses AI to transform natural language descriptions into professional, downloadable invoices instantly.

## The Problem

Creating invoices typically involves:
- Manually entering client information
- Calculating line items and taxes
- Formatting everything professionally
- Generating PDF files
- Keeping track of invoice history

What if you could just type: *"Billed $500 to Acme Corp for 5 hours of consulting at $100/hr, due in 14 days"* and get a perfectly formatted invoice?

That's exactly what I built.

## Tech Stack Overview

I chose a modern, serverless-first architecture that's both fast and cost-effective:

### Frontend
- **React 19** with TypeScript for type safety
- **Vite** for lightning-fast development
- **Zustand** for lightweight state management
- **Tailwind CSS** for rapid styling
- **jsPDF + html2canvas** for PDF/PNG generation
- **Zod** for runtime validation

### Backend
- **Hono.js** on Cloudflare Workers for edge computing
- **Cloudflare D1** for serverless SQLite database
- **Upstash Redis** for caching and session management
- **z.ai GLM** for AI-powered data extraction

## Key Features

### 1. Natural Language Processing
The core magic happens when you describe your invoice in plain English. The AI extracts:
- Client name and contact information
- Line items with quantities and prices
- Tax rates and discounts
- Payment terms and due dates
- Invoice numbers and dates

### 2. Professional Templates
Clean, minimalist invoice design that looks professional out of the box. No need to worry about formatting – the app handles it all.

### 3. Real-time Preview
See your invoice update instantly as you edit. Make changes and see the results immediately.

### 4. Multiple Export Formats
Download invoices in high-quality PDF or PNG format with a single click.

### 5. Session Management
Your invoice history persists across sessions using browser storage, so you can reference past invoices anytime.

### 6. Responsive Design
Works seamlessly on desktop, tablet, and mobile devices.

## Project Architecture

```
smart-invoice-generator/
├── frontend/          # React 19 + Vite + TypeScript
├── backend/           # Hono.js on Cloudflare Workers
├── shared/            # Shared types and Zod schemas
└── README.md
```

The monorepo structure allows sharing types and validation schemas between frontend and backend, ensuring consistency across the entire stack.

## How the AI Magic Works

When you enter a description like:
> "Web development services for StartupXYZ, 20 hours at $75/hr, due in 30 days"

The backend:
1. Sends your text to z.ai's GLM-4 model
2. Extracts structured data using carefully crafted prompts
3. Validates the output against Zod schemas
4. Returns a properly formatted invoice object
5. Stores it in Cloudflare D1 for persistence
6. Caches the result in Redis for faster subsequent access

The AI is surprisingly accurate at understanding various formats and extracting the right information, even with casual language.

## Building the Frontend

I started with a React 19 + Vite template using TypeScript:

```bash
npm create vite@latest smart-invoice-generator-frontend -- --template react-ts
```

The frontend uses Zustand for state management, which keeps things simple without the overhead of Redux:

```tsx
// store/invoiceStore.ts
import { create } from 'zustand';
import { Invoice, InvoiceItem } from '@/shared/types';

interface InvoiceStore {
  invoice: Invoice;
  setInvoice: (invoice: Invoice) => void;
  updateItem: (id: string, item: InvoiceItem) => void;
}

export const useInvoiceStore = create<InvoiceStore>((set) => ({
  invoice: initialInvoice,
  setInvoice: (invoice) => set({ invoice }),
  updateItem: (id, item) => set((state) => ({
    invoice: {
      ...state.invoice,
      items: state.invoice.items.map((i) =>
        i.id === id ? item : i
      ),
    },
  })),
}));
```

For PDF generation, I used jsPDF combined with html2canvas to capture the invoice HTML and convert it to a downloadable PDF:

```tsx
const generatePDF = async () => {
  const element = invoiceRef.current;
  const canvas = await html2canvas(element, { scale: 2 });
  const imgData = canvas.toDataURL('image/png');

  const pdf = new jsPDF('p', 'mm', 'a4');
  const pdfWidth = pdf.internal.pageSize.getWidth();
  const pdfHeight = (canvas.height * pdfWidth) / canvas.width;

  pdf.addImage(imgData, 'PNG', 0, 0, pdfWidth, pdfHeight);
  pdf.save(`invoice-${invoice.invoiceNumber}.pdf`);
};
```

## Building the Backend with Hono.js

For the backend, I chose Hono.js because it's:
- **Fast**: Optimized for Cloudflare Workers
- **Lightweight**: Minimal overhead
- **Type-safe**: Full TypeScript support
- **Modern**: Built for the edge computing era

Here's a simplified version of the invoice generation endpoint:

```typescript
// src/index.ts
import { Hono } from 'hono';
import { zValidator } from '@hono/zod-validator';
import { generateInvoiceSchema } from '../shared/schema';

const app = new Hono<{ Bindings: Env }>();

app.post('/api/invoice/generate', zValidator('json', generateInvoiceSchema), async (c) => {
  const { text, sessionId } = c.req.valid('json');

  // Call AI to extract invoice data
  const extractedData = await extractInvoiceData(text, c.env);

  // Save to database
  const invoice = await saveInvoice(c.env.DB, extractedData, sessionId);

  // Cache in Redis
  await c.env.REDIS.setex(
    `invoice:${invoice.id}`,
    3600,
    JSON.stringify(invoice)
  );

  return c.json({ success: true, invoice, sessionId });
});

export default app;
```

## Deploying to Cloudflare

One of the best parts about this stack is how easy deployment is:

### Backend Deployment
```bash
cd backend
npx wrangler deploy
```

### Frontend Deployment
```bash
cd frontend
npm run build
npx wrangler pages deploy dist
```

Both deployments are free-tier friendly and can handle significant traffic without costs.

## Challenges and Learnings

### 1. AI Accuracy
Getting the AI to consistently extract accurate data required prompt engineering. I learned that:
- Clear examples in the prompt improve accuracy
- Structured output formats (JSON) are essential
- Validation on the frontend and backend catches errors

### 2. PDF Generation
Generating pixel-perfect PDFs in the browser was tricky. I found that:
- html2canvas can blur text at higher resolutions
- Using `scale: 2` helps maintain quality
- Testing across different browsers is crucial

### 3. State Management
Managing invoice state across the application required careful thought:
- Zustand keeps it simple but flexible
- Normalizing state helps with updates
- Local storage provides persistence

### 4. TypeScript Throughout
Using TypeScript in both frontend and backend (with shared types) prevented countless bugs:
- Catch errors at compile time
- Better IDE autocomplete
- Self-documenting code

## What's Next?

I have several ideas for improving the project:

1. **Multi-language Support**: Add i18n for international users
2. **Custom Templates**: Let users customize invoice designs
3. **Recurring Invoices**: Automate monthly billing
4. **Payment Integration**: Add Stripe for direct payments
5. **Email Delivery**: Send invoices directly to clients
6. **Dashboard**: Track paid/unpaid invoices

## Try It Out

The project is open source and ready to use. You can:
- Try the [live demo](https://invoicegenerated.com)

## Conclusion

Building this Smart Invoice Generator was a great learning experience. It combines modern frontend frameworks with edge computing and AI to solve a real problem. The tech stack (React 19, Hono.js, Cloudflare Workers, and AI) is fast, type-safe, and cost-effective.

If you're building something similar, I'd recommend:
- Start with the AI prompt engineering – it's the core feature
- Use TypeScript everywhere – it saves headaches
- Test PDF generation thoroughly – browsers vary
- Keep the state management simple – Zustand over Redux
- Deploy to edge – your users will thank you

Happy coding! 🚀

---

**Tech Stack Summary:**
- Frontend: React 19, Vite, TypeScript, Zustand, Tailwind CSS
- Backend: Hono.js, Cloudflare Workers, D1 Database
- AI: z.ai GLM-4 for natural language processing
- Deployment: Cloudflare Pages + Workers (free tier friendly)
