# Weather Agent Template ⛅

**AI-Powered Weather Assistant using Mastra Framework with Google Gemini**

A comprehensive template demonstrating how to build intelligent weather agents using the Mastra framework. Features current weather lookups, multi-day forecasts, and activity recommendations based on weather conditions.

## 🎯 Overview

The Weather Agent template showcases advanced AI agent development using the Mastra framework, integrating with real-time weather APIs and Google's Gemini model for natural language processing. This template demonstrates both simple weather queries and complex workflow-based activity planning.

## ✨ Key Features

### 🤖 Intelligent Weather Agent
- **Natural Language Processing**: Powered by Google Gemini 1.5 Pro for context-aware responses
- **Location Intelligence**: Automatic geocoding and location translation support
- **Multi-Language Support**: Handles location names in various languages
- **Conversational Interface**: Natural dialogue flow with contextual responses

### 🌦️ Comprehensive Weather Data
- **Current Weather**: Real-time temperature, humidity, wind speed, and conditions
- **Detailed Metrics**: Feels-like temperature, wind gusts, and precipitation data
- **Weather Conditions**: 20+ weather condition types with descriptive labels
- **Location Validation**: Error handling for invalid or ambiguous locations

### 📋 Activity Planning Workflow
- **Weather-Based Recommendations**: AI-generated activity suggestions based on conditions
- **Time-Specific Planning**: Morning and afternoon activity recommendations
- **Indoor Alternatives**: Backup plans for poor weather conditions
- **Location-Specific Activities**: Venue and location recommendations

### 🔧 Mastra Framework Integration
- **Tools**: Custom weather lookup tool with structured input/output
- **Agents**: Configured agents with specific instructions and capabilities
- **Workflows**: Multi-step processes for complex weather planning
- **Logging**: Comprehensive logging with Pino logger integration

## 🚀 Getting Started

### Prerequisites
- Node.js 18+ 
- TypeScript environment
- Google Gemini API access
- Internet connection for weather APIs

### Installation

```bash
# Clone the repository
git clone https://github.com/sorcerai/weather-agent-example.git
cd weather-agent-example

# Install dependencies
npm install

# Set up environment variables
cp .env.example .env
```

### Environment Configuration

```env
# Required: Google Gemini API model
MODEL=gemini-1.5-pro-latest

# Optional: Custom model override
# MODEL=gemini-1.5-flash-latest
```

### Running the Agent

```bash
# Start development server
npm run dev

# Run in production mode
npm start

# Run tests
npm test
```

## 🛠 Technical Architecture

### Core Components

#### Weather Agent (`src/mastra/agents/index.ts`)
```typescript
export const weatherAgent = new Agent({
  name: 'Weather Agent',
  instructions: `
    You are a helpful weather assistant that provides accurate weather information.
    Your primary function is to help users get weather details for specific locations.
  `,
  model: google(process.env.MODEL ?? "gemini-1.5-pro-latest"),
  tools: { weatherTool },
});
```

#### Weather Tool (`src/mastra/tools/index.ts`)
```typescript
export const weatherTool = createTool({
  id: 'get-weather',
  description: 'Get current weather for a location',
  inputSchema: z.object({
    location: z.string().describe('City name'),
  }),
  outputSchema: z.object({
    temperature: z.number(),
    feelsLike: z.number(),
    humidity: z.number(),
    windSpeed: z.number(),
    windGust: z.number(),
    conditions: z.string(),
    location: z.string(),
  })
});
```

#### Weather Workflow (`src/mastra/workflows/index.ts`)
```typescript
const weatherWorkflow = createWorkflow({
  id: 'weather-workflow',
  inputSchema: z.object({
    city: z.string().describe('The city to get the weather for'),
  })
})
.then(fetchWeather)
.then(planActivities);
```

## 🌍 Weather Data Sources

### Open-Meteo API Integration
- **Geocoding**: `https://geocoding-api.open-meteo.com/v1/search`
- **Weather Data**: `https://api.open-meteo.com/v1/forecast`
- **No API Key Required**: Free service with reliable global coverage
- **Real-Time Updates**: Current conditions and hourly forecasts

### Supported Weather Metrics
```typescript
interface WeatherData {
  temperature: number;        // Current temperature (°C)
  feelsLike: number;         // Apparent temperature (°C)
  humidity: number;          // Relative humidity (%)
  windSpeed: number;         // Wind speed (m/s)
  windGust: number;          // Wind gusts (m/s)
  conditions: string;        // Weather condition description
  location: string;          // Resolved location name
}
```

### Weather Condition Mapping
```typescript
const weatherConditions = {
  0: 'Clear sky',
  1: 'Mainly clear',
  2: 'Partly cloudy',
  3: 'Overcast',
  45: 'Foggy',
  51: 'Light drizzle',
  61: 'Slight rain',
  71: 'Slight snow fall',
  95: 'Thunderstorm',
  // ... 20+ condition types
};
```

## 🤖 AI Agent Capabilities

### Natural Language Understanding
- **Location Parsing**: Handles various location formats and languages
- **Context Awareness**: Maintains conversation context across queries
- **Error Handling**: Graceful handling of invalid locations or API errors
- **Clarification Requests**: Asks for missing information when needed

### Response Formatting
The agent provides structured, informative responses including:
- Current weather conditions with all relevant metrics
- Location confirmation and resolution
- Practical advice based on weather conditions
- Appropriate units and formatting for readability

### Multi-Language Support
```typescript
// Example agent instructions
instructions: `
  - If the location name isn't in English, please translate it
  - If giving a location with multiple parts (e.g. "New York, NY"), 
    use the most relevant part (e.g. "New York")
  - Include relevant details like humidity, wind conditions, and precipitation
`
```

## 📋 Activity Planning System

### Workflow-Based Planning
The weather workflow demonstrates complex multi-step processes:

1. **Weather Fetching**: Get current conditions and forecast
2. **Activity Analysis**: AI-powered activity recommendations
3. **Structured Output**: Formatted activity suggestions

### Activity Recommendation Format
```markdown
📅 [Day, Month Date, Year]
═══════════════════════════

🌡️ WEATHER SUMMARY
• Conditions: [brief description]
• Temperature: [X°C/Y°F to A°C/B°F]
• Precipitation: [X% chance]

🌅 MORNING ACTIVITIES
Outdoor:
• [Activity Name] - [Description with location]
  Best timing: [specific time range]
  Note: [weather consideration]

🌞 AFTERNOON ACTIVITIES
Outdoor:
• [Activity Name] - [Description with location]
  Best timing: [specific time range]
  Note: [weather consideration]

🏠 INDOOR ALTERNATIVES
• [Activity Name] - [Description with venue]
  Ideal for: [weather condition]

⚠️ SPECIAL CONSIDERATIONS
• [Weather warnings, UV index, wind conditions]
```

### Activity Intelligence
- **Time-Specific Recommendations**: Different activities for morning/afternoon
- **Weather-Appropriate Suggestions**: Activities matched to current conditions
- **Indoor Alternatives**: Backup plans for poor weather
- **Location-Specific Venues**: Real venue and location recommendations
- **Safety Considerations**: UV warnings, wind alerts, precipitation guidance

## 🔧 Development Guide

### Custom Tool Creation
```typescript
// Example: Creating a forecast tool
export const forecastTool = createTool({
  id: 'get-forecast',
  description: 'Get 7-day weather forecast',
  inputSchema: z.object({
    location: z.string(),
    days: z.number().min(1).max(7).default(5),
  }),
  outputSchema: z.object({
    forecast: z.array(z.object({
      date: z.string(),
      maxTemp: z.number(),
      minTemp: z.number(),
      conditions: z.string(),
    })),
  }),
  execute: async ({ context }) => {
    // Implementation
  },
});
```

### Agent Customization
```typescript
// Example: Travel planning agent
export const travelAgent = new Agent({
  name: 'Travel Weather Planner',
  instructions: `
    You are a travel planning expert who specializes in weather-based 
    itinerary recommendations. Consider seasonal patterns, local climate, 
    and weather-dependent activities when making suggestions.
  `,
  model: google('gemini-1.5-pro-latest'),
  tools: { weatherTool, forecastTool },
});
```

### Workflow Extensions
```typescript
// Example: Multi-city comparison workflow
const travelPlanningWorkflow = createWorkflow({
  id: 'travel-planning',
  inputSchema: z.object({
    cities: z.array(z.string()),
    travelDates: z.object({
      start: z.string(),
      end: z.string(),
    }),
  }),
})
.then(fetchWeatherMultipleCities)
.then(compareConditions)
.then(recommendBestDestination);
```

## 📊 Usage Examples

### Simple Weather Query
```typescript
// Using the weather agent directly
const response = await weatherAgent.run([
  {
    role: 'user',
    content: 'What\'s the weather like in Tokyo?'
  }
]);

console.log(response);
// Output: Detailed weather information for Tokyo with current conditions
```

### Activity Planning Workflow
```typescript
// Using the weather workflow
const result = await weatherWorkflow.run({
  city: 'San Francisco'
});

console.log(result.activities);
// Output: Structured activity recommendations based on SF weather
```

### Batch Processing
```typescript
// Processing multiple locations
const cities = ['New York', 'London', 'Tokyo', 'Sydney'];
const weatherPromises = cities.map(city => 
  weatherAgent.run([{
    role: 'user',
    content: `Current weather in ${city}`
  }])
);

const results = await Promise.all(weatherPromises);
```

## 🌟 Advanced Features

### Error Handling & Validation
```typescript
// Location validation
if (!geocodingData.results?.[0]) {
  throw new Error(`Location '${location}' not found`);
}

// Weather code fallback
function getWeatherCondition(code: number): string {
  const conditions: Record<number, string> = {
    // ... condition mappings
  };
  return conditions[code] || 'Unknown';
}
```

### Logging & Monitoring
```typescript
// Mastra logger configuration
export const mastra = new Mastra({
  workflows: { weatherWorkflow },
  agents: { weatherAgent },
  logger: new PinoLogger({
    name: 'WeatherAgent',
    level: 'info',
  }),
});
```

### Response Streaming
```typescript
// Streaming responses for long-form content
const response = await agent.stream([
  { role: 'user', content: prompt }
]);

for await (const chunk of response.textStream) {
  process.stdout.write(chunk);
}
```

## 🚀 Deployment Options

### Local Development
```bash
# Run with hot reloading
npm run dev

# Access agent at localhost:3000
curl -X POST http://localhost:3000/api/weather \
  -H "Content-Type: application/json" \
  -d '{"location": "San Francisco"}'
```

### Production Deployment
```bash
# Build for production
npm run build

# Start production server
npm start

# Environment variables for production
export MODEL=gemini-1.5-pro-latest
export NODE_ENV=production
```

### Docker Deployment
```dockerfile
FROM node:18-alpine

WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

COPY . .
RUN npm run build

EXPOSE 3000
CMD ["npm", "start"]
```

## 🔌 API Integration Examples

### REST API Endpoint
```typescript
// Express.js integration example
app.post('/api/weather', async (req, res) => {
  try {
    const { location } = req.body;
    const result = await weatherAgent.run([
      { role: 'user', content: `Weather in ${location}` }
    ]);
    res.json({ weather: result });
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});
```

### WebSocket Integration
```typescript
// Real-time weather updates
io.on('connection', (socket) => {
  socket.on('get-weather', async (location) => {
    try {
      const result = await weatherAgent.run([
        { role: 'user', content: `Weather in ${location}` }
      ]);
      socket.emit('weather-update', result);
    } catch (error) {
      socket.emit('weather-error', error.message);
    }
  });
});
```

### Chatbot Integration
```typescript
// Discord bot example
client.on('messageCreate', async (message) => {
  if (message.content.startsWith('!weather ')) {
    const location = message.content.slice(9);
    const response = await weatherAgent.run([
      { role: 'user', content: `Weather in ${location}` }
    ]);
    await message.reply(response);
  }
});
```

## 🔧 Customization Guide

### Adding New Weather Sources
```typescript
// Example: Adding WeatherAPI.com integration
const weatherAPITool = createTool({
  id: 'weather-api-lookup',
  description: 'Get weather from WeatherAPI.com',
  inputSchema: z.object({
    location: z.string(),
    apiKey: z.string(),
  }),
  execute: async ({ context }) => {
    const url = `https://api.weatherapi.com/v1/current.json?key=${context.apiKey}&q=${context.location}`;
    // Implementation
  },
});
```

### Custom Activity Categories
```typescript
// Example: Sports-specific activity planning
const sportsAgent = new Agent({
  name: 'Sports Weather Advisor',
  instructions: `
    Provide weather-based sports activity recommendations.
    Consider wind conditions for sailing, temperature for running,
    precipitation for outdoor sports, and visibility for cycling.
  `,
  model: google('gemini-1.5-pro-latest'),
  tools: { weatherTool, sportsVenuesTool },
});
```

### Multi-Language Support
```typescript
// Example: Localized weather responses
const localizedAgent = new Agent({
  name: 'Multilingual Weather Agent',
  instructions: `
    Respond in the user's preferred language.
    Use local units (Celsius/Fahrenheit) based on location.
    Include culturally relevant activity suggestions.
  `,
  model: google('gemini-1.5-pro-latest'),
  tools: { weatherTool, translationTool },
});
```

## 📈 Performance Optimization

### Caching Strategies
```typescript
// Redis caching for weather data
const cachedWeatherTool = createTool({
  id: 'cached-weather',
  execute: async ({ context }) => {
    const cacheKey = `weather:${context.location}`;
    const cached = await redis.get(cacheKey);
    
    if (cached) {
      return JSON.parse(cached);
    }
    
    const weatherData = await getWeather(context.location);
    await redis.setex(cacheKey, 300, JSON.stringify(weatherData)); // 5min cache
    return weatherData;
  },
});
```

### Rate Limiting
```typescript
// API rate limiting
import rateLimit from 'express-rate-limit';

const weatherLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // limit each IP to 100 requests per windowMs
  message: 'Too many weather requests, please try again later.',
});

app.use('/api/weather', weatherLimiter);
```

### Parallel Processing
```typescript
// Concurrent weather fetching for multiple locations
const fetchMultipleLocations = async (locations: string[]) => {
  const promises = locations.map(location => 
    weatherTool.execute({ context: { location } })
  );
  
  const results = await Promise.allSettled(promises);
  return results.map((result, index) => ({
    location: locations[index],
    data: result.status === 'fulfilled' ? result.value : null,
    error: result.status === 'rejected' ? result.reason : null,
  }));
};
```

## 🧪 Testing

### Unit Tests
```typescript
// Jest testing example
describe('Weather Tool', () => {
  test('should fetch weather for valid location', async () => {
    const result = await weatherTool.execute({
      context: { location: 'New York' }
    });
    
    expect(result).toHaveProperty('temperature');
    expect(result).toHaveProperty('conditions');
    expect(result.location).toContain('New York');
  });
  
  test('should handle invalid location', async () => {
    await expect(
      weatherTool.execute({ context: { location: 'InvalidCity123' } })
    ).rejects.toThrow('Location \'InvalidCity123\' not found');
  });
});
```

### Integration Tests
```typescript
// Agent conversation testing
describe('Weather Agent', () => {
  test('should provide weather information', async () => {
    const response = await weatherAgent.run([
      { role: 'user', content: 'What\'s the weather in Paris?' }
    ]);
    
    expect(response).toContain('Paris');
    expect(response).toMatch(/temperature|temp/i);
  });
});
```

### Load Testing
```typescript
// Performance testing with multiple concurrent requests
const loadTest = async () => {
  const concurrentRequests = 100;
  const promises = Array.from({ length: concurrentRequests }, () =>
    weatherAgent.run([
      { role: 'user', content: 'Weather in London' }
    ])
  );
  
  const startTime = Date.now();
  await Promise.all(promises);
  const endTime = Date.now();
  
  console.log(`Processed ${concurrentRequests} requests in ${endTime - startTime}ms`);
};
```

## 🔧 Troubleshooting

### Common Issues

#### API Rate Limiting
```bash
# Error: Too many requests to weather API
# Solution: Implement caching and request throttling
```

#### Location Not Found
```bash
# Error: Location 'XYZ' not found
# Solution: Implement fuzzy matching and suggestion system
```

#### Model Token Limits
```bash
# Error: Token limit exceeded
# Solution: Implement response chunking and summarization
```

### Debug Configuration
```typescript
// Enable debug logging
export const mastra = new Mastra({
  logger: new PinoLogger({
    name: 'WeatherAgent',
    level: 'debug', // Enable detailed logging
  }),
});
```

## 🚀 Future Enhancements

### Planned Features
- [ ] Historical weather data analysis
- [ ] Weather alerts and notifications
- [ ] Climate change impact analysis
- [ ] Integration with calendar systems
- [ ] Weather-based business intelligence

### Advanced Integrations
- [ ] Satellite imagery integration
- [ ] Air quality monitoring
- [ ] UV index and health recommendations
- [ ] Agricultural weather insights
- [ ] Marine and aviation weather

### AI Improvements
- [ ] Personalized activity recommendations
- [ ] Learning from user preferences
- [ ] Predictive weather impact analysis
- [ ] Multi-modal weather visualization
- [ ] Voice interaction capabilities

## 🤝 Contributing

We welcome contributions to improve the Weather Agent template!

### Development Setup
1. Fork the repository
2. Create a feature branch (`git checkout -b feature/weather-alerts`)
3. Commit your changes (`git commit -m 'Add weather alerts'`)
4. Push to the branch (`git push origin feature/weather-alerts`)
5. Open a Pull Request

### Contribution Guidelines
- Follow TypeScript best practices
- Add tests for new features
- Update documentation for API changes
- Ensure backward compatibility
- Add example usage for new tools

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- **Mastra Framework** for the excellent agent development platform
- **Open-Meteo** for providing free weather API access
- **Google Gemini** for advanced language model capabilities
- **TypeScript community** for robust type safety
- **Open source contributors** for continuous improvements

---

**Built with ❤️ using the Mastra Framework and Google Gemini**

*Empowering developers to create intelligent weather agents with natural language understanding and real-time data integration.*

## 📞 Contact

- **Issues**: GitHub Issues for bug reports and feature requests
- **Discussions**: GitHub Discussions for questions and community
- **Documentation**: Comprehensive guides in the `/docs` folder
- **Examples**: Additional examples in the `/examples` directory

---

*Weather Agent Template - Your gateway to intelligent weather applications*