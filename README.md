# 🎨 Shirt Configurator

<div align="center">

**A visually stunning, real-time 3D shirt customization platform**

[![Next.js](https://img.shields.io/badge/Next.js-14.2-black?style=for-the-badge&logo=next.js)](https://nextjs.org/)
[![React](https://img.shields.io/badge/React-18.2-blue?style=for-the-badge&logo=react)](https://reactjs.org/)
[![Three.js](https://img.shields.io/badge/Three.js-0.160-black?style=for-the-badge&logo=three.js)](https://threejs.org/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.3-blue?style=for-the-badge&logo=typescript)](https://www.typescriptlang.org/)
[![Express](https://img.shields.io/badge/Express-4.18-green?style=for-the-badge&logo=express)](https://expressjs.com/)

[Features](#-features) • [Architecture](#-architecture) • [Quick Start](#-quick-start) • [Documentation](#-documentation) • [Testing](#-testing)

</div>

---

## ✨ Features

### 🎯 Core Functionality
- **Real-time 3D Visualization** - Interactive Three.js-powered 3D shirt rendering
- **Material Library** - Extensive collection of PBR materials with previews
- **Color Customization** - Advanced color picker with recent colors
- **Print Placement** - Drag-and-drop print/image placement with zone-based positioning
- **Text Tool** - Custom text printing with style controls
- **Pattern Application** - Apply patterns with blend modes and intensity control
- **Component Isolation** - Select and customize individual shirt components
- **Export Capabilities** - Export configurations as images or 3D models

### 🎨 Visual Excellence
- **Vibrant Dark Theme** - Electric cyan, magenta, and emerald accents
- **Glass Morphism UI** - Modern translucent panels with backdrop blur
- **Smooth Animations** - Framer Motion-powered micro-interactions
- **Responsive Design** - Mobile-first approach with adaptive layouts
- **Loading States** - Stunning animated loading screens
- **Error Handling** - Beautiful 404 pages and error boundaries

### ⚡ Performance
- **Code Splitting** - Lazy loading for optimal bundle size
- **Image Optimization** - Next.js Image component with WebP/AVIF
- **3D Optimization** - Efficient Three.js rendering with LOD support
- **State Management** - Zustand for lightweight, performant state
- **Caching Strategy** - Intelligent caching for materials and textures

### 🧪 Quality Assurance
- **TypeScript** - Full type safety across the codebase
- **ESLint** - Code quality and consistency
- **Modular Architecture** - Component-based, maintainable structure
- **Error Tracking** - Comprehensive logging and error handling
- **Accessibility** - ARIA labels, keyboard navigation, focus management

---

## 🏗️ Architecture

### System Overview

```
┌─────────────────────────────────────────────────────────────┐
│                      Client (Next.js)                        │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │  3D Viewer   │  │  Configurator│  │   Print      │      │
│  │  (Three.js) │  │   Controls   │  │   Tools      │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
│         │                │                    │             │
│         └────────────────┴────────────────────┘             │
│                          │                                    │
│                    Zustand Store                              │
└──────────────────────────┼────────────────────────────────────┘
                           │
                    ┌──────▼──────┐
                    │   Express    │
                    │    Server    │
                    └──────┬──────┘
                           │
        ┌──────────────────┼──────────────────┐
        │                  │                   │
   ┌────▼────┐      ┌──────▼──────┐    ┌──────▼──────┐
   │ Cloudflare│     │   File Upload │    │   API Routes │
   │    R2     │     │   (Multer)    │    │   (REST)     │
   └──────────┘     └──────────────┘    └──────────────┘
```

### Technology Stack

**Frontend:**
- **Framework**: Next.js 14.2 (App Router)
- **3D Rendering**: Three.js 0.160 + React Three Fiber 8.15
- **State Management**: Zustand 4.5
- **Styling**: Tailwind CSS 3.4 + Custom Design System
- **Animations**: Framer Motion 12.23
- **Language**: TypeScript 5.3

**Backend:**
- **Framework**: Express 4.18
- **Storage**: Cloudflare R2 (S3-compatible)
- **File Upload**: Multer 1.4
- **Language**: TypeScript 5.3

**Development:**
- **Package Manager**: pnpm
- **Build Tool**: Next.js (client), TypeScript Compiler (server)
- **Containerization**: Docker + Docker Compose

---

## 🚀 Quick Start

### Prerequisites

- **Node.js** 18+ 
- **pnpm** 8+ (`npm install -g pnpm`)
- **Docker** (optional, for containerized deployment)

### Installation

```bash
# Clone the repository
git clone <repository-url>
cd shirt_config

# Install dependencies
cd client && pnpm install
cd ../server && pnpm install
```

### Environment Setup

**Client** (`.env.local`):
```env
# Optional: Add any client-specific environment variables
NEXT_PUBLIC_API_URL=http://localhost:3001
```

**Server** (`.env`):
```env
# Cloudflare R2 Configuration
R2_ACCOUNT_ID=your_account_id
R2_ACCESS_KEY_ID=your_access_key
R2_SECRET_ACCESS_KEY=your_secret_key
R2_BUCKET_NAME=your_bucket_name
R2_PUBLIC_URL=https://your-bucket.r2.dev

# Server Configuration
PORT=3001
NODE_ENV=development
```

### Development

```bash
# Terminal 1: Start client (port 3000)
cd client
pnpm dev

# Terminal 2: Start server (port 3001)
cd server
pnpm dev
```

Visit **http://localhost:3000** to see the application.

### Production Build

```bash
# Build client
cd client
pnpm build
pnpm start

# Build server
cd server
pnpm build
pnpm start
```

### Docker Deployment

```bash
# Build and run with Docker Compose
docker-compose up --build

# Or run individually
docker build -t shirt-configurator-client ./client
docker build -t shirt-configurator-server ./server
```

---

## 📁 Project Structure

```
shirt_config/
├── client/                 # Next.js frontend application
│   ├── app/                # Next.js App Router pages
│   ├── components/         # React components
│   │   ├── configurator/  # Configurator UI components
│   │   ├── viewer/         # 3D viewer components
│   │   ├── prints/         # Print management components
│   │   ├── layout/         # Layout components
│   │   └── ui/             # Reusable UI primitives
│   ├── lib/                # Utility libraries
│   ├── stores/             # Zustand state stores
│   ├── hooks/              # Custom React hooks
│   ├── types/              # TypeScript type definitions
│   └── data/               # Static data (materials, zones)
│
├── server/                 # Express backend API
│   ├── src/
│   │   ├── controllers/    # Request handlers
│   │   ├── routes/         # API route definitions
│   │   ├── services/       # Business logic
│   │   └── utils/          # Utility functions
│   └── scripts/            # Utility scripts
│
└── docs/                   # Documentation
```

---

## 📚 Documentation

- **[Client README](./client/README.md)** - Frontend architecture and setup
- **[Server README](./server/README.md)** - Backend API and services
- **[UI Philosophy](./client/docs/UI_PHILOSOPHY.md)** - Design system and principles
- **[Testing Guide](./TESTING.md)** - Testing strategy and best practices
- **[Contributing](./CONTRIBUTING.md)** - Development guidelines

---

## 🧪 Testing

### Testing Strategy

We follow industry best practices for testing:

- **Unit Tests** - Component and utility function testing
- **Integration Tests** - API endpoint and data flow testing
- **E2E Tests** - Full user workflow testing
- **Performance Tests** - Load time and rendering performance
- **Accessibility Tests** - WCAG compliance verification

See [TESTING.md](./TESTING.md) for detailed testing guidelines.

### Running Tests

```bash
# Client tests
cd client
pnpm test

# Server tests
cd server
pnpm test

# E2E tests
pnpm test:e2e
```

---

## 🎯 Key Features Deep Dive

### 3D Rendering
- Real-time material swapping
- Dynamic lighting presets
- Camera position presets
- Print zone visualization
- Component highlighting and isolation

### State Management
- Undo/Redo functionality
- Design state persistence
- Optimistic UI updates
- History management

### Performance Optimizations
- Code splitting and lazy loading
- Image optimization
- 3D model compression
- Efficient re-rendering
- Memory management

---

## 🔧 Configuration

### Design Tokens

The application uses a centralized design system defined in `client/lib/design/tokens.ts`:

- **Colors**: Vibrant dark theme with electric accents
- **Typography**: Inter font family with consistent scale
- **Spacing**: 8px base unit system
- **Shadows**: Elevation-based shadow system
- **Animations**: Consistent timing and easing

### 3D Configuration

- **Model Format**: GLB/GLTF
- **Texture Format**: WebP, PNG, JPEG
- **Rendering**: WebGL with fallbacks
- **Performance**: LOD support, frustum culling

---

## 🚢 Deployment

### Vercel (Client)

The client is optimized for Vercel deployment:

```bash
# Deploy to Vercel
vercel deploy
```

### Server Deployment

The server can be deployed to any Node.js hosting platform:

- **Railway**
- **Render**
- **Fly.io**
- **AWS EC2**
- **DigitalOcean**

See server README for detailed deployment instructions.

---

## 🤝 Contributing

We welcome contributions! Please read [CONTRIBUTING.md](./CONTRIBUTING.md) for:

- Code style guidelines
- Commit message conventions
- Pull request process
- Testing requirements

---

## 📄 License

This project is proprietary and confidential.

---

## 👥 Team

Built with ❤️ by the Jersey Shore development team.

---

<div align="center">

**Made with cutting-edge technology and attention to detail**

[Report Issues](https://github.com/your-repo/issues) • [Request Features](https://github.com/your-repo/issues) • [Documentation](./docs/)

</div>

