# BroilerPro Developer Guide

This guide provides information for developers who want to contribute to or extend the BroilerPro broiler farm management system.

## Development Setup

### Prerequisites

- Node.js (v16 or higher)
- npm or yarn
- Git

### Local Development Environment

1. Clone the repository
   ```bash
   git clone https://github.com/yourusername/broiler-farm-app.git
   cd broiler-farm-app
   ```

2. Install dependencies
   ```bash
   npm install
   # or
   yarn install
   ```

3. Start the development server
   ```bash
   npm run dev
   # or
   yarn dev
   ```

4. Open your browser and navigate to `http://localhost:5173` (or the port shown in your terminal)

## Project Structure

```
broiler-farm-app/
├── src/                      # Source code
│   ├── app.css               # Global CSS
│   ├── app.html              # HTML template
│   ├── components/           # Reusable components
│   │   ├── dashboard/        # Dashboard components
│   │   ├── flock/            # Flock components
│   │   └── layout/           # Layout components
│   └── routes/               # SvelteKit routes (pages)
│       ├── +layout.svelte    # Main layout
│       ├── +page.svelte      # Dashboard (home)
│       └── ...               # Feature-specific pages
├── static/                   # Static assets
├── tests/                    # Tests
├── .gitignore                # Git ignore file
├── jsconfig.json             # JavaScript configuration
├── package.json              # Package configuration
├── README.md                 # Project readme
├── svelte.config.js          # Svelte configuration
└── vite.config.js            # Vite configuration
```

## Coding Standards

### JavaScript/Svelte

- Use ES6+ features
- Follow Svelte's component structure
- Use reactive declarations for derived state
- Keep components focused on a single responsibility

### CSS

- Use component-scoped CSS (Svelte's built-in styling)
- Follow BEM naming convention for global styles
- Use CSS variables for theming and consistency

### Naming Conventions

- **Components**: PascalCase (e.g., `FlockCard.svelte`)
- **Files/Directories**: kebab-case (e.g., `flock-management`)
- **Variables/Functions**: camelCase (e.g., `getFlockData`)
- **CSS Classes**: kebab-case (e.g., `flock-card`)

## Adding New Features

### Creating a New Page

1. Create a new directory in `src/routes` (e.g., `src/routes/new-feature/`)
2. Add a `+page.svelte` file in the new directory
3. Implement the page content
4. Update the sidebar in `src/components/layout/Sidebar.svelte` to include the new page

### Creating a New Component

1. Identify which feature the component belongs to
2. Create a new file in the appropriate directory (e.g., `src/components/flock/NewComponent.svelte`)
3. Implement the component
4. Import and use the component where needed

## State Management

BroilerPro uses Svelte's built-in state management capabilities:

- **Component State**: Local state within components
- **Stores**: For shared state across components
- **Context API**: For component hierarchies

Example of creating a store:

```javascript
// src/stores/flockStore.js
import { writable } from 'svelte/store';

export const flocks = writable([]);

export function addFlock(flock) {
  flocks.update(items => [...items, flock]);
}

export function removeFlock(id) {
  flocks.update(items => items.filter(item => item.id !== id));
}
```

## API Integration

Currently, BroilerPro uses mock data. When integrating with a real API:

1. Create service files in a new `src/services` directory
2. Use the Fetch API or a library like Axios for HTTP requests
3. Handle loading states and errors appropriately

Example service file:

```javascript
// src/services/flockService.js
const API_URL = 'https://api.example.com';

export async function getFlocks() {
  try {
    const response = await fetch(`${API_URL}/flocks`);
    if (!response.ok) throw new Error('Failed to fetch flocks');
    return await response.json();
  } catch (error) {
    console.error('Error fetching flocks:', error);
    throw error;
  }
}
```

## Testing

### Unit Testing

Use Vitest for unit testing:

```bash
npm install --save-dev vitest
```

Example test:

```javascript
// src/components/flock/FlockCard.test.js
import { describe, it, expect } from 'vitest';
import { render } from '@testing-library/svelte';
import FlockCard from './FlockCard.svelte';

describe('FlockCard', () => {
  it('renders correctly with props', () => {
    const flock = { id: '1', name: 'Test Flock', age: 21 };
    const { getByText } = render(FlockCard, { props: { flock } });
    
    expect(getByText('Test Flock')).toBeTruthy();
    expect(getByText('21 days')).toBeTruthy();
  });
});
```

### End-to-End Testing

Use Playwright for end-to-end testing:

```bash
npm install --save-dev @playwright/test
```

## Building for Production

```bash
npm run build
# or
yarn build
```

The build output will be in the `build` directory, ready for deployment.

## Deployment

BroilerPro can be deployed to various platforms:

### Static Hosting (Netlify, Vercel, etc.)

1. Connect your GitHub repository to the hosting platform
2. Configure the build settings:
   - Build command: `npm run build`
   - Publish directory: `build`
3. Deploy

### Self-Hosted

1. Build the application
2. Copy the contents of the `build` directory to your web server
3. Configure your web server to serve the application

## Troubleshooting

### Common Issues

1. **Module not found errors**
   - Check import paths
   - Ensure dependencies are installed

2. **Styling issues**
   - Check for CSS conflicts
   - Ensure styles are scoped properly

3. **Build errors**
   - Check the console for specific error messages
   - Verify SvelteKit and Vite configurations

### Getting Help

- Check the [SvelteKit documentation](https://kit.svelte.dev/docs)
- Search for issues on the project's GitHub repository
- Reach out to the development team
