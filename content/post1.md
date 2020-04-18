title: NJ Covid-19 Map
date: 2020-04-10
category: Test

# NJ Covid-19

Avinci club is cool!

Yes!

<div id="container2" style="height: 50%; width: 100%; margin: 0 auto;">
  Downloading chart...
</div>

<div id="container" style="height: 50%; width: 100%; margin: 0 auto; text-align:center; line-height: 520px">
  Downloading map...
</div>


<script src="https://code.highcharts.com/maps/highmaps.js"></script>
<script src="https://code.highcharts.com/maps/modules/data.js"></script>
<script src="https://code.highcharts.com/maps/modules/exporting.js"></script>
<script src="https://code.highcharts.com/maps/modules/offline-exporting.js"></script>
<script src="https://code.highcharts.com/mapdata/countries/us/us-nj-all.js"></script>

<script>

Highcharts.getJSON(
  'https://services1.arcgis.com/0MSEUqKaxRlEPj5g/ArcGIS/rest/services/COVID19_Counties_View/FeatureServer/0/query?where=state_fips%3D34&objectIds=&time=&geometry=&geometryType=esriGeometryEnvelope&inSR=&spatialRel=esriSpatialRelIntersects&resultType=none&distance=0.0&units=esriSRUnit_Meter&returnGeodetic=false&outFields=label%2Cconfirmed%2C+confirmed_per_100k%2Cpopulation_2017%2C+fips%2Ccounty%2Cstate_fips&returnGeometry=false&returnCentroid=false&featureEncoding=esriDefault&multipatchOption=xyFootprint&maxAllowableOffset=&geometryPrecision=&outSR=&datumTransformation=&applyVCSProjection=false&returnIdsOnly=false&returnUniqueIdsOnly=false&returnCountOnly=false&returnExtentOnly=false&returnQueryGeometry=false&returnDistinctValues=false&cacheHint=false&orderByFields=&groupByFieldsForStatistics=&outStatistics=&having=&resultOffset=&resultRecordCount=&returnZ=false&returnM=false&returnExceededLimitFeatures=true&quantizationParameters=&sqlFormat=none&f=pjson&token=',
  function (dataIn) {
    var data = dataIn.features;
    data.forEach(function(d) {
      d.label = d.attributes.label;
      d.value = d.attributes.confirmed_per_100k;
      d.name = d.attributes.county;
      d.y = d.attributes.confirmed;
      if (d.attributes.fips >= 10000){
        d.code1 = '' + d.attributes.fips;      
      } else if (d.attributes.fips >= 1000){
        d.code1 = '0' + d.attributes.fips; 
      }
 else if (d.attributes.fips >= 1000){
        d.code1 = '0' + d.attributes.fips; 
      }

    })
    
 Highcharts.chart('container2', {
    chart: {
        plotBackgroundColor: null,
        plotBorderWidth: null,
        plotShadow: false,
        type: 'pie'
    },
    title: {
        text: 'NJ COVID-19 By County'
    },
    tooltip: {
        pointFormat: '{series.name}: <b>{point.y:.0f}</b>'
    },
    accessibility: {
        point: {
            valueSuffix: '%'
        }
    },
    plotOptions: {
        pie: {
            allowPointSelect: true,
            cursor: 'pointer',
            dataLabels: {
                enabled: true,
                format: '<b>{point.name}</b>: {point.percentage:.0f} %',
                connectorColor: 'silver'
            }
        }
    },
    series: [{
        name: 'Confirmed Cases',
        data: data,
    }]
});   
    /**
     * Data parsed from http://www.bls.gov/lau/#tables
     *
     * 1. Go to http://www.bls.gov/lau/laucntycur14.txt (or similar, updated
     *  datasets)
     * 2. In the Chrome Developer tools console, run this code:
     *  copy(JSON.stringify(document.body.innerHTML.split('\n').filter(function (s) { return s.indexOf('<PUT DATE HERE IN FORMAT e.g. Feb-14>') !== -1; }).map(function (row) { row = row.split('|'); return { code: 'us-' + row[3].trim().slice(-2).toLowerCase() + '-' + row[2].trim(), name: row[3].trim(), value: parseFloat(row[8]) }; })))
     * 3. The data is now on your clipboard, paste it below
     * 4. Verify that the length of the data is reasonable, about 3300
     *  counties.
     */

    var countiesMap = Highcharts.geojson(
        Highcharts.maps['countries/us/us-nj-all']
      );
      // Filter out the state borders and separator lines, we want these
      // in separate series
  
    // Add state acronym for tooltip

    document.getElementById('container').innerHTML = 'Rendering map...';

    // Create the map
    setTimeout(function () { // Otherwise innerHTML doesn't update
      Highcharts.mapChart('container', {
        chart: {
          borderWidth: 1,
          marginRight: 20 // for the legend
        },

        title: {
          text: 'NJ COVID-19 Cases/100K'
        },

        legend: {
          layout: 'vertical',
          align: 'right',
          floating: true,
          backgroundColor: ( // theme
            Highcharts.defaultOptions &&
            Highcharts.defaultOptions.legend &&
            Highcharts.defaultOptions.legend.backgroundColor
          ) || 'rgba(255, 255, 255, 0.85)'
        },

        mapNavigation: {
          enabled: true
        },

        colorAxis: {
          min: 0,
          max: 700,
          tickInterval: 100,
          stops: [[0, '#F1EEF6'], [0.5, '#900037'], [1, '#500007']],
          labels: {
            format: '{value}'
          }
        },
        

        plotOptions: {
          mapline: {
            showInLegend: false,
            enableMouseTracking: false
          }
        },

        series: [{
          mapData: countiesMap,
          data: data,
          joinBy: ['fips', 'code1'],
          name: 'COVID-19 Cases',
          tooltip: {
            valueSuffix: '/100K'
          },
          dataLabels: {
             enabled: true,
              color: '#FFFFFF',
              format: '{point.name}<br>{point.value}'
         },
          borderWidth: 0.5,
          states: {
            hover: {
              color: '#a4edba'
            }
          },
          shadow: false
        }]
      });
    }, 0);
  }
);

</script>