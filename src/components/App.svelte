<main>
    <div class="overlay">
        <label id="label">2000</label>
        <input id="year-slider" type="range" min="2000" max="2015" value="2000" />
    </div>
    <div id="map"></div>

</main>

<script>
    import * as d3 from "d3";
    mapboxgl.accessToken = "pk.eyJ1Ijoia3F0cmFuIiwiYSI6ImNsc2t6eXQ4czA3dmcyanJ5eWhoaWQxeHIifQ.2kwe8rDh1r-X61ULATh_Jg";
    
    const geojsonUrl = "static/world_energy_data2.geojson";
    let geojsonData;

    d3.json(geojsonUrl).then(data => {
        geojsonData = data;
        initializeMap();
    });

    // const map = new mapboxgl.Map({
    //         container: "map",
    //         style: "mapbox://styles/mapbox/light-v11" , //mapbox://styles/kqtran/clsp8c8fo000601qz77zx4z2t", 
    //         center: [-79.035728, 35.932522], // Chapel Hill Public Library
    //         zoom: 2,
    //         minZoom: 1,
    //         maxZoom: 15,
    //     });

    async function initializeMap(){
        //create mapbox map
        const map = new mapboxgl.Map({
        container: "map",
        style: "mapbox://styles/mapbox/light-v11" , //mapbox://styles/kqtran/clsp8c8fo000601qz77zx4z2t", 
        center: [-79.035728, 35.932522], // Chapel Hill Public Library
        zoom: 2,
        minZoom: 1,
        maxZoom: 15,
    });

        const response = await fetch(geojsonUrl);
        geojsonData = await response.json();

        const slider = document.getElementById('year-slider')
        slider.addEventListener('input', ()=>{
            updateLabel();
            updateMap(map);
        })
        updateLabel()
        updateMap()

    }

    function updateLabel(){
            const slider = document.getElementById('year-slider');
            const label = document.getElementById('label');
            label.textContent = slider.value;
    }

        // function to update map based on slider value
    function updateMap(map){
        const selectedYear = parseInt(document.getElementById('year-slider').value);
        const aggregatedData = aggregateDataByYear(selectedYear);
        plotChoroplethMap(map, aggregatedData)
    
    }

    function aggregateDataByYear(selectedYear){
        const aggregatedData = {};

        geojsonData.features.forEach(feature => {
            // const properties = feature.properties;
            const country = feature.properties.country;
            const year = feature.properties.year;

            if (year === selectedYear){

                if (!aggregatedData[country]){
                    aggregatedData[country] = {gdp: 0, geometry: feature.geometry};
                }
                aggregatedData[country] += feature.properties.gdp;
            } 
        });
        console.log(aggregatedData)
        return aggregatedData;
    }

    function plotChoroplethMap(map, data){
        if (map.getSource('countries')) {
            map.removeSource('countries');
        }
        if (map.getLayer('countries-fill')) {
            map.removeLayer('countries-fill');
        }
        // map.getSource('countries') && map.removeSource('countries');
        // map.getLayer('countries-fill') && map.removeLayer('countries-fill');

        // Add GeoJSON source and layer
        map.addSource('countries', {
            type: 'geojson',
            data:  {
            type: 'FeatureCollection',
            features: Object.values(data).map(d => ({
                type: 'Feature',
                geometry: d.geometry,
                properties: { gdp: d.gdp }
                }))
            }
        });
        

        map.addLayer({
            id: 'countries-fill',
            type: 'fill',
            source: 'countries',
            layout: {},
            paint: {
            'fill-color': [
                'interpolate',
                ['linear'],
                ['get', 'gdp'],
                0, '#f7f7f7',
                1000000000, '#6EED8C',
                5000000000, '#4DCD7D',
                10000000000, '#31B057',
                50000000000, '#238D45',
                100000000000, '#005A32'
            ],
            'fill-opacity': 0.8
            }
        });
    }
    
    // let aggregatedData = aggregateDataByYear(2015)

   

    // let slider_label = ""
    initializeMap()

    
    // function aggregateDataByYear(selectedYear){
    //     const aggregatedData = {};

    //     geojsonData.features.forEach(feature => {
    //         const properties = feature.properties;
    //         const country = properties.country;
    //         const year = properties.year;

    //         if (year === selectedYear){

    //             if (!aggregatedData[country]){
    //                 aggregatedData[country] = {gdp: 0, geometry: feature.geometry};
    //             }
    //             aggregatedData[country] += properties.gdp;
    //         } 
    //     });

    //     return aggregatedData;
    // }
    // let aggregatedData = aggregateDataByYear(2015)

   
    


    // // https://d2ad6b4ur7yvpq.cloudfront.net/naturalearth-3.3.0/ne_110m_admin_0_countries.geojson'
    // map.on('load', function  () {
    //     map.addSource('countries', {
    //         type: 'geojson',
    //         data: 'static/world_energy_data2.geojson'
    //     });
        
    //     // let desiredYear = 2010;

    //     // map.getSource('countries').setData({
    //     //     type: 'FeatureCollection',
    //     //     features: geojson.features.filter(function (feature) {
    //     //         // Assuming each feature has a "properties" object containing information about the feature, including the year
    //     //         return feature.properties.year === desiredYear;
    //     //     })
    //     // });

    
    //     map.addLayer({
    //         'id': 'countries',
    //         'type': 'fill',
    //         'source': 'countries',
    //         'layout': {},
    //         'paint': {
    //             'fill-color': ['interpolate',
    //             ['linear'],
    //             ['get', 'gdp'], // Property in GeoJSON representing GDP
    //             0, '#FFEDA0', // Define color stops based on GDP values
    //             10000000, '#FED976',
    //             50000000, '#FEB24C',
    //             100000000, '#FD8D3C',
    //             500000000, '#FC4E2A',
    //             1000000000, '#E31A1C',
    //             5000000000, '#BD0026',
    //             10000000000, '#800026'],
    //             'fill-opacity': 0.7 // Opacity
    //             }
    //      });
    // });
    let slider_time = 2015;
	let currYear = "";


</script>



