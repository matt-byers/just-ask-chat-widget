# Just Ask ✨
Just Ask is an AI-first component library for building chat-based, personalised search and refinement user experiences for web apps. It aims to rethink the way we build search experiences by extracting rich customer profiles and hyper-specific product requirements, and using them to create a more effective search experience.

> ⚠️ **Note:** This project is still under development. NPM packages are not yet published, and components are being added progressively.

This project consists of 3 NPM packages:
- Frontend React components (@chat-widget/frontend)
- Node.js backend server (@chat-widget/backend) 
- Shared TypeScript utilities (@chat-widget/utils)

# What is our mission?
When was the last time you struggled to buy something online? What were the specific things that you were looking for in the product? What was your motivation behind wanting that specific thing in the first place? Now tell me, what did you actually search for when you started looking for it? You probably typed in one or two keywords right?

Search today is broken. You type in 3 or 4 keywords to describe something you want, then spend hours manually scrolling through options to find what you want. Imagine a world where you could actually describe all the unique factors that define what you want, and then having that thing picked out for you straight away. This world is possible now.

Just Ask aims to bring search into the AI age by delivering a radically simple and more effective search experience. By extracting rich customer profiles and hyper-specific product requirements, our vision is to create a framework for solving the user-side of the search equation by understanding user intent better than any existing solution.

# How does it work?

1. Integrate the chatbot into your React frontend with details about your business, what you're offering, and what your search criteria are.
2. The user chats with your chatbot and explains what they're looking for.
3. The chatbot knows to extract your search criteria first, then ask about the customer's preferences, likes, dislikes.
4. Throughout the conversation, the widget builds a rich personal profile of the user.
5. This profile is the magic sauce we use to create a personalised experiences such as:
  - AI generated content which highlights how a listing matches the user's preferences.
  - Rewriting descriptions to reflect the user's preferences.

# Quick Start 🚀
To demo the project, clone the repo and set up a `/backend/.env` file with the following:

```
# API Keys
OPENAI_API_KEY={your openai api key}

# Server Configuration
PORT=5001

# Security Settings (Development)
ALLOWED_ORIGINS=http://localhost:3000
```

# Components 🧩

### Chat
The main chat widget component that provides an AI-powered chat interface with automatic search data extraction.

#### Screenshot
![Chat Widget Screenshot](./screenshots/chat_screenshot.png)
*The screenshot above shows chat widget component with the search data and customer intention profile JSON objects that have been extracted from the conversation.*

#### Props
```typescript
interface ChatProps {
  searchConfig: SearchConfigSchema;        // Required - Defines search fields and validation
  requireManualSearch?: boolean;           // Optional - Disable automatic search (default: false)
}
```

#### Features
- Streams AI chat responses in real-time
- Automatically extracts search data from conversations
- Builds customer intention profile (likes, dislikes, priorities)
- Accessible with ARIA labels and keyboard navigation
- Error boundary protected, so your app doesn't break if the chat widget throws an error.

#### Basic Setup & Integration
```tsx
import { Chat, useChatStore } from '@chat-widget/frontend';
import { SearchConfigSchema } from '@chat-widget/types';

// 1. Define your search configuration
const searchConfig: SearchConfigSchema = {
  searchData: {
    location: {
      type: 'string',
      description: 'City or region to search in',
      required: true,
      example: 'Paris, France'
    },
    startDate: {
      type: 'string',
      description: 'Travel start date',
      required: true,
      format: 'YYYY-MM-DD',
      example: '2024-06-01'
    },
    endDate: {
      type: 'string',
      description: 'Travel end date',
      required: true,
      format: 'YYYY-MM-DD',
      example: '2024-06-07'
    }
  }
};

const App: React.FC = () => {
  // 2. Access chat store values
  const { 
    requiredSearchData,    // Contains all required search fields
    customerIntention,     // Customer intention data
    syncSearchState        // Function to sync search state
  } = useChatStore();

  // 3. Handle required search data updates
  useEffect(() => {
    if (Object.keys(requiredSearchData).length > 0) {
      // Perform search when all required fields are present
      performYourSearch(requiredSearchData);
    }
  }, [requiredSearchData]);

  // 4. Track customer intentions
  useEffect(() => {
    if (Object.keys(customerIntention).length > 0) {
      // Handle customer intention updates
      handleIntentionChanges(customerIntention);
    }
  }, [customerIntention]);

  // 5. Example of manual search integration
  const handleUserManualSearch = (params: SearchParams) => {
    // Keep chat in sync when search is triggered from your UI
    syncSearchState(params);
    performYourSearch(params);
  };

  // 6. Render the chat widget
  return (
    <Chat 
      searchConfig={searchConfig}
      requireManualSearch={false}
    />
  );
};
```

### CustomTag
A component that generates and displays AI-optimized content tags based on customer preferences and item data.

#### Screenshot
![Custom Tag Screenshot](./screenshots/custom_tag_screenshot.png)
*The screenshot above shows a custom tag component looped over holiday destinations listings with the `strongMatchOnly` prop set to `true`. This is why Maldives has a custom tag, while Paris does not, because the user's preferences are more strongly aligned with Maldives.*


#### Props
```typescript
interface CustomTagProps {
  itemInformation: Record<string, any>;    // Required - Product/item data
  name: string;                            // Required - Identifier for the tag
  instructions: string;                    // Required - Generation instructions
  textExamples?: string[];                // Optional - Example outputs for guidance
  updateOnIntentionChange?: boolean;      // Optional - Regenerate on customer intention changes
  strongMatchOnly?: boolean;              // Optional - Only show strong preference matches
  backgroundColor?: string;               // Optional - Custom background color
  borderColor?: string;                   // Optional - Custom border color
}
```

#### Features
- Generates personalized content based on customer intention and item data
- Automatically updates when customer preferences change
- Customisable background and border colors
- Debounced content generation
- Error boundary protected

#### Usage
```tsx
import { CustomTag } from '@chat-widget/frontend';

function ProductFeature({ product }) {
  return (
    <CustomTag 
      itemInformation={product}
      name="feature-highlight"
      instructions="Generate a compelling feature highlight"
      updateOnIntentionChange={true}
      backgroundColor="#f0f0f0"
    />
  );
}
```

### ErrorBoundary
A component that catches and handles React rendering errors gracefully, so the components don't break your app.

#### Props
```typescript
interface ErrorBoundaryProps {
  children: React.ReactNode;               // Required - Child components to protect
}
```

#### Features
- Prevents entire app crashes from component errors
- Logs errors to console for debugging
- Returns null on error to maintain app stability

#### Usage
```tsx
import { ErrorBoundary } from '@chat-widget/frontend';

function App() {
  return (
    <ErrorBoundary>
      <Chat searchConfig={searchConfig} />
    </ErrorBoundary>
  );
}
```

