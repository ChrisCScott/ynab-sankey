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

// Load Google's Sankey chart-drawing code. (drawChart is a callback)
GoogleCharts.load(drawChart, {
    'packages': ['sankey']
});

function disambiguatedCategoryNameForMonth(category, month) {
    return category.name + " " + month.month
}

function diffsToFlows(diffs, month, month_next) {
    // Match each category with a positive diff (i.e. an increase
    // between month and month_next) with one or more categories with
    // negative diffs. Use the following priority:
    //      0. Same category: Before applying diffs, flow
    //      1. Exact match: If this category's diff is the same
    //         (except for differing sign) as another category's
    //         diff, match them.
    //      2. 
    // Prioritize exact matches; e.g. a category that is reduced by
    // $100 next month should be matched with a category that is
    // increased by $100 next month.
}

function monthsToSankeyData(months) {
    // Take an array of months and turn it into an array of
    // (category1, category2, flow) tuples. category1 and category2 should
    // correspond to consecutive months and should thus be disambiguated by
    // month (and year, if there are more than 12 months).
    // For example, ("Food 2020-01-01", "Food 2020-02-01", 10000) is a valid tuple.
    // Each month object has the following form:
    /*
    {
      "month": "string",
      "note": "string",
      "income": 0,
      "budgeted": 0,
      "activity": 0,
      "to_be_budgeted": 0,
      "age_of_money": 0,
      "deleted": true,
      "categories": [
        {
          "id": "string",
          "category_group_id": "string",
          "name": "string",
          "hidden": true,
          "original_category_group_id": "string",
          "note": "string",
          "budgeted": 0,
          "activity": 0,
          "balance": 0,
          "goal_type": "TB",
          "goal_creation_month": "string",
          "goal_target": 0,
          "goal_target_month": "string",
          "goal_percentage_complete": 0,
          "deleted": true
        }
    }
    */
   // `flows` will record the flows to be shown between categories/months in the
   // Sankey diagram.
   var flows = []
   for(var i = 0; i < months.length - 1; i++) {
       let month = months[i]
       let month_next = months[i+1]
       var diffs = []
       // Populate `diffs` with {category, category_next, diff} pairs
       month.categories.forEach(category => {
           // Find the same category in the next month:
           let category_next = month_next.categories.find(element => element.id == category.id)
           // We should always be able to find `category_next`, since each category
           // should be present in every month. But just in case, check to see
           // whether it was actually found and, if not, replace with `null`:
           category_next = typeof category_next == 'undefined' ? null : category_next
           // Treat a missing category as having $0 budgeted:
           let budgeted_next = category_next == null ? 0 : category_next.budgeted
           // Add an {category, category_next, diff} pair to `diffs`:
           diffs.push({
               category: category,
               category_next: typeof category_next == 'undefined' ? null : category_next,
               diff: budgeted_next - category.budgeted
           })
       });
       // Now we have {id, diff} pairs for each category in `month`
       // (where `diff` represents the change between month and month_next).
       // We now need to transform this into an array of flows in the form
       // `[ from, to, weight ]`, where `from` is a category in this month,
       // `to` is a category in the next month, and `weight` is the flow.
       flows.push(...diffsToFlows(diffs, month, month_next)) // Keep `flows` flat.
   }
   // Leave it to the calling code to convert this array to a suitable format
   return flows
}

// Once the GoogleCharts library is loaded, we will draw a Sankey chart here:
function drawChart() {
    // Define data with which to draw chart:
    const data_arr = monthsToSankeyData(months)
    // A DataTable object is required:
    const data_tab = GoogleCharts.api.visualization.arrayToDataTable()
    // Instantiate the Sankey chart:
    const chart = new GoogleCharts.api.visualization.Sankey(document.getElementById('sankey-chart'))
    // Draw the chart:
    chart.draw(data_tab)
}

export default {
  props: ['months'],
  methods: {
    // Now we can make this method available to our template
    // So we can format this milliunits in the correct currency format
    convertMilliUnitsToCurrencyAmount: utils.convertMilliUnitsToCurrencyAmount
  }
}
</script>
