# Frontend SDK Assignment — Insight Component

## Objective
- Build a React-based frontend SDK, publish it to npm, and demonstrate it in a separate React app.
- Design clean abstractions that handle data contracts and transformations.
- Focus on developer experience while keeping the SDK reusable beyond UI components.

## Part 1: SDK Setup
1. Create the SDK with React and publish it to npm so it can be installed in other React projects.
2. Ensure the SDK is installable via a standard package manager.
3. Build a sample React application that consumes the SDK and uses its components.

## Part 2: SDK API
The SDK exposes a single component: `<Insight />`.

```ts
type InsightType = "trend" | "contributor";
type TimeGrain = "daily" | "weekly" | "monthly";

interface InsightProps {
  type: InsightType;
  metric: string;
  dimension?: string;
  timeGrain: TimeGrain;
  timeRange: number; // number of days from today (e.g., last 30 days)
  dataResolver: (
    metric: string,
    grain: TimeGrain,
    fromTime: Date,
    toTime: Date
  ) => Promise<any[]>;
  dimensionValuesResolver?: (
    metric: string,
    dimension: string
  ) => Promise<string[]>;
}
```

## Part 3: Data Contracts

### 1) Trend Insight
- **Resolver input**
```json
{
  "fromtime": "01-01-2025",
  "totime": "01-02-2025",
  "metric": "Revenue",
  "grain": "weekly",
  "type": "Trend"
}
```
- **Resolver output**
```json
[
  { "fromtime": "01-01-2025", "totime": "07-01-2025", "Revenue": 123 },
  { "fromtime": "08-01-2025", "totime": "14-01-2025", "Revenue": 235 },
  ...
]
```
- **Expected visualization**
  - Line / Area / Bar chart
  - X-axis: time
  - Y-axis: metric value

 <img width="auto" height="300" alt="image" src="https://github.com/user-attachments/assets/b4fbb635-f1f7-45ae-8874-f289edadf997" />


### 2) Contributor Insight
- `dimension` prop is mandatory when `type = "contributor"`.
- **Resolver input**
```json
{
  "fromtime": "01-01-2025",
  "totime": "01-02-2025",
  "metric": "Revenue",
  "dimension": "location",
  "grain": "weekly",
  "type": "Contributor"
}
```
- **Resolver output**
```json
[
  { "fromtime": "01-01-2025", "totime": "07-01-2025", "India": 63, "China": 23, "USA": 43 },
  { "fromtime": "08-01-2025", "totime": "14-01-2025", "India": 32, "China": 56, "USA": 16 },
  ...
]
```

### Example: contributor insight with `dimensionValuesResolver`

```jsx
import { Insight } from "your-sdk";

const dataResolver = async (metric, grain, fromTime, toTime) => {
  // return series data per time bucket
  return [
    { fromtime: "01-01-2025", totime: "07-01-2025", India: 63, China: 23, USA: 43 },
    { fromtime: "08-01-2025", totime: "14-01-2025", India: 32, China: 56, USA: 16 },
    ...
  ];
};

const dimensionValuesResolver = async (metric, dimension) => {
  // return the possible dimension values to render each series
  return ["India", "China", "USA"];
};

export default function ContributorExample() {
  return (
    <Insight
      type="contributor"
      metric="Revenue"
      dimension="location"
      timeGrain="weekly"
      timeRange={30}
      dataResolver={dataResolver}
      dimensionValuesResolver={dimensionValuesResolver}
    />
  );
}
```

- **Expected visualization**
  - Stacked bar or multi-line chart
  - Each dimension value is a separate series

<img width="auto" height="300" alt="image" src="https://github.com/user-attachments/assets/695b5e48-7366-476f-a242-d3a8b1ecc13f" />


## Part 4: SDK Responsibilities
- Calculate `fromTime` and `toTime` internally using `timeRange`.
- Call the provided resolvers.
- Transform raw data into chart-ready formats.
- Select the visualization based on the insight type.
- Handle loading and empty states gracefully.

### The consuming app should not
- Format data.
- Choose charts.
- Handle time calculations.

## Part 5: Sample App
In a separate React application:
1. Install the SDK.
2. Implement (mocking is fine):
   - `dataResolver`
   - `dimensionValuesResolver`
3. Render at least:
   - One Trend insight
   - One Contributor insight

## Expectations & Evaluation Criteria
- Clean abstraction and component design.
- Clear path to expand with more insight types and visualizations.
- Strong SDK usability and developer experience.
- Thoughtful data transformation.
- Code clarity and structure (this is about quality, not the number of charts).

## Submission
Share:
1. npm package link.
2. GitHub repository (SDK) + Demo video (Sample app + SDK).
3. README describing how to use the SDK.
