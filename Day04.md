# Day 4

See <https://www.youtube.com/watch?v=Yk-unX4KnV4>

Data set here: <https://www.kaggle.com/datasets/kenjee/ken-jee-youtube-data>

* Start by asking: what's the use case? For business dashbaords, usually value daded
* Dashboard-making will be iterative

Steps:
* Create clean conda environment
* `pip install streamlit plotly spyder`
* Write the dashboard code:
    * Imports
    * Load data function (`@st.cache` decorator)
    * Build dashboard
        * Sidebar for navigation using `st.sidebar.selectbox`
    * Feature engineering
        * Goal is to have a baseline (12 month MA) and current (6 month MA)
        * Use `pd.DateOffset` to get most recent date minus 12 months (1 yr period)
        * Get median for all columns, all values in 1 year period dataframe

Aggregate videos dashboard page:
* Creating aggregate vs individual pages:
    * Use if statement (getting value of sidebar selectbox) to populate diff. pages with diff. content 
    * Additional data engineering within the if statement
    * Use `st.metric()` to create a widget that shows a number (and optionally a delta)
    * Use `st.columns()` to create columns to place the metric widgets into
    * When populating a column, you can say `with column:` and then any call to `st.metric()` will 
      add that metric to that column
    * Use `st.dataframe()` to display a dataframe as a table
* Styling the dataframe:
    * Pandas library has a concept of a "styler"
    * Can define functions that will return a style if a dataframe cell meets certain criteria
    * Can apply them to the entire dataframe with `df.style.applymap(style_negative)`
    * To apply multiple styles, chain `.applymap()` calls together
    * Formatting as percents:
        * Iterate over each numerical column, create a map of column names to a function: `"{:.1%}.format"`
        * (That function will format the input as a percentage, rounding it to 1 decimal point)
        * Then, add a final call in the chain of `.applymap()` that is a final call to `.format(pctmap)`
        * (Yikes... Typical pandas hairball.)
* (Done with aggregate video analysis dashboard page!)

Individual video dashboard page:
* Start with drop-down to select videos:
    * Use `st.selectbox()` for the drop-down, use list of videos from dataframe converted to tuple
* Bar chart:
    * Use plotly to create the bar chart figure, with a function applied to the country code column to group by
      US/India/Other
    * To add a plotly figure, use `st.plotly_chart(fig)`
* Last figure is the most complex:
    * Time series (over 30 days?) of 80th/50th/20th percentile
    * Requires combining two dataframes
    * `pd.merge()` (with various details)
    * To get daily view data, requires `pd.pivot_table()`
    * To get final value being plotted, use `df[:, ['median_views', '80pct_views', '20pct_views']].cumsum()`
    * Using `plotly.graph_objects` for this chart, meaning have to add individual lines, `go.Scatter()` with `mode='lines'`

