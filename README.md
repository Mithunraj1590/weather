# weather

A Vue.js weather dashboard application that displays weather information based on user-selected cities. The dashboard includes current weather conditions, temperature, and weather descriptions that adapt based on the time of day and weather conditions.

## Features

- City selection dropdown to choose different cities.
- Current weather information including temperature, weather description, and sunset time.
- Adaptive weather description based on time of day and weather conditions.
- Date range selection for historical weather data (integration not detailed here).

## Technologies Used

- Vue.js
- Axios (for API requests)
- OpenWeatherMap API
- Tailwind CSS (for styling)

## Project Setup

To get started with this project, follow these steps:

### 1. Clone the Repository

```bash
git clone https://github.com/yourusername/weather-dashboard.git
cd weather-dashboard
npm install or yarn install
npm run dev or yarn dev


create a .env file in the root folder and add this "VITE_API_KEY=618f66329067ec213c2fa04518664ac7"



<-----------------CHALLENGES----------------->

1. Limited Access to Historical Data: I’m facing a hurdle because accessing historical weather data requires a paid API version, which I might not be able to afford. This limitation means I need to adjust my feature set or find alternative ways to offer historical insights.

2. Date Range Selector Plugin Availability: Finding a Vue.js date range selector plugin that matches my design requirements has been difficult. If I can’t find a suitable plugin, I’ll have to create a custom one, which could be quite time-consuming and complex.

3. Custom Date Range Selector Development: Developing a custom date range selector from scratch is challenging and will take significant time. I’ll need to handle everything from date validation to user interface design and integration with the weather data fetching logic.

4. UI/UX Considerations: Making sure the custom date range selector and other UI elements are user-friendly and fit my design requirements will require careful attention and testing.