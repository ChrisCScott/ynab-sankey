<template>
  <div class="container">
    <h5>Budget Flows</h5>
    <!-- This div will get populated via Javascript: -->
    <div class="sankey" id="sankey-chart">
    </div>
  </div>
</template>

<script>

import {utils} from 'ynab';
import {GoogleCharts} from 'google-charts';

// Load Google's Sankey chart-drawing code:
GoogleCharts.load(drawChart, {
    'packages': ['sankey']
});

function drawChart() {
    
}

export default {
  props: ['transactions'],
  methods: {
    // Now we can make this method available to our template
    // So we can format this milliunits in the correct currency format
    convertMilliUnitsToCurrencyAmount: utils.convertMilliUnitsToCurrencyAmount
  }
}
</script>
