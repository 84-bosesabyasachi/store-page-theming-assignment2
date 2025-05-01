





1. **Menu Bar Navigation**
   - Create a navigation menu bar with two options:
     - Home (already implemented)
     - Themes (to be created)

2. **Themes Page**
   - Create a new "Themes" page at `src/views/Home/themes/`
   - This page should display all available themes
   - Allow users to preview and select different themes

3. **Multiple Theme Implementation**
   - Implement 3 themes in total:
     - Default theme (already exists)
     - 2 new themes focusing on different medical specialties (e.g., Organ Transplant, Cosmetic Surgery)

4. **Theme Configuration**
   - Use Zustand for state management
   - Configure Zustand to persist theme selection in localStorage
   - Apply the selected theme globally throughout the application

5. **Theme Components**
   - Each theme should include:
     - Color scheme variations
     - Typography changes
     - UI element styling (buttons, cards, forms)
     - Custom hero sections
     - Custom menu bar styling
    


### Directory Structure

```
src/
├── @types/
│   └── theme.ts              # Add specialty theme types
├── assets/
│   └── styles/
│       ├── app.css           # Main CSS file
│       └── themes.css        # Theme CSS variables
├── components/
│   ├── shared/
│   │   └── ThemeSelector.tsx # Theme switching component
│   └── template/
│       └── ThemeProvider.tsx # Theme provider component
├── configs/
│   └── theme.config.ts       # Update theme configuration
├── store/
│   └── themeStore.ts         # Extend Zustand theme store
└── views/
    └── Home/
        ├── components/       # Update existing components
        │   ├── GetInTouch.tsx
        │   ├── Home.tsx
        │   └── ...
        ├── themes/           # Create theme specific components
        │   ├── base/         # Default theme
        │   │   ├── colors.ts
        │   │   └── typography.ts
        │   ├── theme1/       # First new theme
        │   │   ├── colors.ts
        │   │   └── typography.ts
        │   └── theme2/       # Second new theme
        │       ├── colors.ts
        │       └── typography.ts
        └── index.tsx
```



Extend the existing theme store in `src/store/themeStore.ts` to include specialty themes:

```typescript

type ThemeState = Theme & {
  specialty: 'default' | 'theme1' | 'theme2';
}

type ThemeAction = {
  // ... existing actions
  setSpecialty: (payload: ThemeState['specialty']) => void;
}

export const useThemeStore = create<ThemeState & ThemeAction>()(
  persist(
    (set) => ({
      // ... existing state
      specialty: 'default',
      setSpecialty: (payload) => set(() => ({ specialty: payload })),
    }),
    {
      name: 'theme',
    },
  ),
)
```

### Theme Provider Implementation

Create a ThemeProvider component to apply theme CSS variables:

```typescript
import React, { useEffect } from 'react'
import { useThemeStore } from '@/store/themeStore'

const ThemeProvider: React.FC<{ children: React.ReactNode }> = ({ children }) => {
  const { specialty } = useThemeStore()
  
  useEffect(() => {
    // Apply CSS class based on selected theme
    document.documentElement.className = `theme-${specialty}`
  }, [specialty])
  
  return <>{children}</>
}

export default ThemeProvider
```

### Tailwind Configuration

Update the Tailwind configuration to use CSS variables for theme colors:

```javascript
// tailwind.config.cjs
module.exports = {
  // ...existing config
  theme: {
    extend: {
      colors: {
        'primary': 'var(--primary)',
        'primary-deep': 'var(--primary-deep)',
        'primary-mild': 'var(--primary-mild)',
        // ...additional theme colors
      },
    },
  },
}
```

### Menu Bar Implementation

Create a navigation menu component:

```tsx
// src/components/shared/MenuBar.tsx
import React from 'react'
import { Link, useLocation } from 'react-router-dom'
import { useThemeStore } from '@/store/themeStore'
import ThemeSelector from './ThemeSelector'

const MenuBar: React.FC = () => {
  const location = useLocation()
  const { specialty } = useThemeStore()
  
  return (
    <nav className={`bg-primary text-white p-4 ${specialty === 'theme1' ? 'theme1-nav' : specialty === 'theme2' ? 'theme2-nav' : ''}`}>
      <div className="max-w-7xl mx-auto flex justify-between items-center">
        <div className="flex space-x-4">
          <Link to="/" className={`px-3 py-2 rounded-md ${location.pathname === '/' ? 'bg-primary-deep' : ''}`}>
            Home
          </Link>
          <Link to="/themes" className={`px-3 py-2 rounded-md ${location.pathname === '/themes' ? 'bg-primary-deep' : ''}`}>
            Themes
          </Link>
        </div>
        <ThemeSelector />
      </div>
    </nav>
  )
}

export default MenuBar




