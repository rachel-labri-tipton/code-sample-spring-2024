# Zoom Feature for Quartz Solar app with Recharts Library (React)

A feature that I worked on at **Open Climate Fix** was on the **Quartz Solar**. The app provides data visualization for solar generation forecasts in the UK. The code I'll be talking about can be found here: [Quartz Solar Code for Chart](https://github.com/openclimatefix/quartz-frontend/blob/main/apps/nowcasting-app/components/charts/remix-line.tsx)

To contextualize this discussion, here's a screenshot of the app: 

![](src/images/Screenshot%202024-05-13%20at%2017.07.22.png)

## Goal 
Enhance the solar generation forecast chart by letting a user zoom in on the data they are viewing. 

**Timeframe**

The **Zoom Feature** went through several iterations from December 2023 - March 2024. It was occasionally put on hold for other work but is now in production. 

## Feature Brief 

Our app had to do the following:

* **Allow a user to visually select data** 
* **Zoom in on selected data** - They would be able to zoom in on one chart and set the zoom level for four charts in the app.
* **Allow user to zoom out**

## **Technologies** Used

- **[React](https://reactjs.org/)** 
- **[Recharts Library](https://recharts.org/en-US)**
- **[Tailwind](https://tailwindcss.com/)** - This is the CSS library for the app.
- Git/GitHub
- VSCode
- TypeScript 

### This is what the feature looks like in production: 
![](src/images/May-13-2024%2018-06-12.gif)



## Research 
*The **Recharts Library** doesn't have a built-in Zoom Feature, so I researched other chart libraries, such as **Plotly**. 
*I brought my research to the team, and we decided to use the tools in the Recharts Library to construct the feature, but it would take more in-depth knowledge of the library. 

## Development

* As a React Library, Recharts comes with a set of out-of-the-box functions, of which `Zoom` is not one, so createing this feature was a matter of research and patching functions together. 

* Once the team decided to stay with Recharts, I researched how others had gotten the chart to seem like it zoomed in and out of the data. Our app formatted data for the chart and prepped data for the charts displayed. The main feature would be filtering the prepared data, and displaying that data on the chart.  

* To better illustrate this point, I've included some code below. 

## Relevant Variables 

<details>
<summary>Relevant Variables</summary>

```

  const preppedData = data.sort((a, b) => a.formattedDate.localeCompare(b.formattedDate));
  const defaultZoom = { x1: "", x2: "" };
  const [filteredPreppedData, setFilteredPreppedData] = useState(preppedData);
  const [globalZoomArea, setGlobalZoomArea] = useGlobalState("globalZoomArea");
  const [globalIsZooming, setGlobalIsZooming] = useGlobalState("globalChartIsZooming");
  const [globalIsZoomed, setGlobalIsZoomed] = useGlobalState("globalChartIsZoomed");
  const [temporaryZoomArea, setTemporaryZoomArea] = useState(defaultZoom);

```
</details>


## Reference Area

<details>
<summary>Reference Area</summary>

```
{zoomEnabled && globalIsZooming && (
            <ReferenceArea
              x1={globalZoomArea?.x1}
              x2={globalZoomArea?.x2}
              fill="#FFD053"
              fillOpacity={0.3}
              xAxisId={"x-axis"}
              yAxisId={"y-axis"}
            />
          )}

```
</details>

## Mouse Events with Recharts
<details>
<summary>onMouseDown</summary>

```
onMouseDown={(e?: { activeLabel?: string }) => {
            if (!zoomEnabled) return;
            setTemporaryZoomArea(globalZoomArea);
            setGlobalIsZooming(true);
            let xValue = e?.activeLabel;
            if (typeof xValue === "string" && xValue.length > 0) {
              setGlobalZoomArea({ x1: xValue, x2: xValue });
            }
          }}
```
</details>

<details>
<summary>onMouseMove</summary>

```
onMouseMove={(e?: { activeLabel?: string }) => {
            if (!zoomEnabled) return;

            if (globalIsZooming) {
              let xValue = e?.activeLabel;
              if (!xValue) return;
              setGlobalZoomArea((zoom) => ({ ...zoom, x2: xValue || "" }));
            }
          }}

```
</details>
 
<details>
<summary>onMouseUp</summary>

```
onMouseUp={(e?: { activeLabel?: string }) => {
            if (!zoomEnabled) return;

            if (globalIsZooming) {
              if (globalZoomArea.x1 === globalZoomArea.x2 && e?.activeLabel && setTimeOfInterest) {
                setGlobalZoomArea(temporaryZoomArea);
                setTimeOfInterest(e?.activeLabel);
              } else if (globalZoomArea?.x1?.length && globalZoomArea?.x2?.length) {
                let { x1 } = globalZoomArea;
                let x2 = e?.activeLabel || "";
                if (x1 > x2) {
                  [x1, x2] = [x2, x1];
                }
                setGlobalZoomArea({ x1, x2 });
                setGlobalIsZoomed(true);
              }
              setGlobalIsZooming(false);
            }
          }}

```
</details>

<details>
<summary>useEffect</summary>

```
useEffect(() => {
    if (!zoomEnabled) return;

    if (!globalIsZooming) {
      const { x1, x2 } = globalZoomArea;

      if (!x1 || !x2) return;

      const dataInAreaRange = preppedData.filter(
        (d) => d?.formattedDate >= x1 && d?.formattedDate <= x2
      );
      setFilteredPreppedData(dataInAreaRange);
      setGlobalZoomArea({ x1: "", x2: "" });
    }
  }, [globalZoomArea, globalIsZooming, preppedData, zoomEnabled]);

```
</details>

<details>
<summary>handleZoomOut</summary>

```

function handleZoomOut() {
    setGlobalIsZoomed(false);
    setFilteredPreppedData(preppedData);
  }

```
</details>


## Wins

## Challenges

### Technical Challenges

* 

* 

*  


    
## Key learnings 

## If I'd had more time...

#### If I'd had more time, I'd have liked to have done the following: 

- Create a feature that selected not only on the X-Axis but also the Y-Axis, but users hadn't necessarily asked for this feature.  




