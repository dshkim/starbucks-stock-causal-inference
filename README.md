# Causal Inference on Starbucks Market Value

## Introduction

### Background

In August 2024, Starbucks announced a change in its CEO. This announcement was significant as it occurred independently of other major events, such as earnings reports or product launches, which could otherwise confound its impact. Leadership changes are closely watched by investors and analysts, as they can potentially influence a company's market value and future performance.

Given the challenges and ethical concerns associated with conducting a controlled experiment to measure the effects of such events, causal inference models provide an alternative approach. These models use existing data to estimate the impact of events like CEO announcements on stock prices, offering valuable insights without the need for experimental manipulation.

### Purpose

The purpose of this project is to estimate the causal effect of Starbucks' CEO change announcement on its market value using multiple causal inference models. Specifically, the project aims to:

- **Event Study Analysis**: Evaluate one-day returns on the event date to capture immediate market reactions.
- **Placebo Testing**: Conduct placebo tests using historical data to determine if similar effects could occur by random chance.
- **Comparative Analysis**: Recognize that while one-day returns provide an immediate snapshot, other methodologies such as synthetic controls or extended event windows might offer more comprehensive insights.

By applying these techniques, the project demonstrates how causal inference can be utilized to analyze significant corporate events and their impact on market value. This approach helps to understand investor reactions and informs business decisions in situations where controlled experiments are not feasible.

## Data Collection

### Data Source

The data for this project was obtained using the Yahoo Finance API. This API provides a convenient way to access historical stock price data and other financial metrics.

### Data Extraction

The dataset includes stock price information for Starbucks (ticker symbol: SBUX) and several of its competitors. The extraction covered the period from January 1, 2024, to August 15, 2024. This timeframe was chosen to capture a sufficient amount of data before and after the event of interest.

The key event date for this analysis was August 13, 2024, the day of the CEO announcement. To assess the impact of this announcement, it was important to compare Starbucks' stock performance to that of its competitors and broader market indices.

### Competitors Included

In addition to Starbucks, data was collected for the following companies and indices:

- **VOO**: Vanguard S&P 500 ETF, representing the broader market.
- **QSR**: Restaurant Brands International, a direct competitor.
- **YUM**: Yum! Brands, another major player in the fast-food industry.
- **MCD**: McDonald's, a key competitor in the fast-food sector.

### Data Storage

After extracting the data, it was saved in CSV format. This format was chosen for its simplicity and ease of use in subsequent analysis. The CSV files facilitate straightforward data manipulation and integration with various analysis tools, particularly for the causal inference part of the project.

The CSV files contain daily stock prices, including opening, closing, high, low, and volume data, which are essential for performing the event study and causal inference analyses.

View my notebook with detailed steps here:
[extract.ipynb](extract.ipynb)

## Estimated Event Study Analysis

### Overview

An event study is an empirical analysis that examines the impact of a significant catalyst occurrence or contingent event on the value of a security, such as company stock [(Investopedia, 2024)](https://www.investopedia.com/terms/e/eventstudy.asp). In this project, we utilized Event Study Analysis to evaluate the impact of Starbucks' CEO announcement on its stock price.

### Steps Taken

1. **Data Preparation**:
   - The data was first reorganized into a wide-format DataFrame. Each column represented the daily returns of different stocks, including Starbucks (SBUX) and its competitors (MCD, QSR, VOO, YUM).
   - We converted the date column to a datetime format to facilitate time-based operations and filtering.
   - <img width="502" alt="image" src="https://github.com/user-attachments/assets/115df781-c7ce-4ccd-9a36-af08b8e88cf6">


2. **Define Analysis Window**:
   - A 120-day window prior to the event date (August 13, 2024) was defined to capture stock returns leading up to and including the event date. This period allowed us to analyze normal stock behavior and compare it to the impact of the announcement.

3. **Create Event Indicator**:
   - An indicator variable was created to mark the event date. This variable was set to 1 on the event day and 0 otherwise. This setup helped isolate the effect of the CEO announcement from other stock price movements.

4. **Fit Regression Model**:
   - An Ordinary Least Squares (OLS) regression model was fitted with Starbucks' returns as the dependent variable and the returns of its competitors and market indices as independent variables, including the event indicator.
   - The model results showed a significant positive coefficient for the event variable. This indicates that, on the day of the CEO announcement, Starbucks' stock price increased significantly compared to what would be expected based on its competitors and market conditions.
   - <img width="582" alt="image" src="https://github.com/user-attachments/assets/3723ae1b-30d6-4bba-b754-b09a36d69432">

5. **Estimate Impact**:
   - The analysis estimated an increase in Starbucks' market value of approximately $19,955,241,349 due to the CEO announcement. This substantial increase reflects a positive market reaction to the leadership change.

6. **Placebo Testing**:
   - To validate the results, placebo tests were conducted by applying the same analysis to various pre-event dates. This step simulated the effect of the CEO announcement occurring at different times to ensure the observed impact was not due to random fluctuations. As we can see from the graph below, the placebo estimates are centered around 0 with relatively small variance. However, the event impact value that we saw of 0.2287 is siginificantly off to the right, which indicate a valid effect.
   - Additionally, the root mean square error (RMSE) from these placebo tests was 0.025, indicating that the observed effect was not likely due to chance.
   - ![image](https://github.com/user-attachments/assets/b65f58d3-f1c9-4f87-8453-0c21d3048a9c)


### Interpretation

The Event Study Analysis revealed a significant positive impact of the CEO announcement on Starbucks' stock price. The estimated gain of nearly $20 billion suggests that investors viewed the leadership change favorably, leading to a considerable increase in the company’s market value. The placebo tests confirmed the robustness of these findings, demonstrating that the observed effect was not attributable to random market movements.

This analysis highlights the effectiveness of Event Study Analysis in quantifying the impact of significant corporate events and provides valuable insights into how such announcements can influence stock prices.

View my notebook with detailed steps here:
[causal-inference.ipynb](causal-inference.ipynb)

## Difference-in-Differences

### Overview

Difference-in-Differences (DiD) is a statistical technique used to estimate the causal effect of an intervention or event by comparing the changes in outcomes over time between a treated group and a control group. This method is particularly useful for analyzing the impact of events when random assignment is not feasible. In this project, we applied DiD to evaluate the effect of Starbucks' CEO announcement on its stock price relative to its competitors.

### Steps Taken

1. **Define Analysis Period and Filter Data**:
   - A 180-day period leading up to and including the event date (August 13, 2024) was defined to analyze stock returns before and after the announcement.
   - The dataset was filtered to include only returns within this period and excluded rows with missing returns.

2. **Create Treatment and Time Variables**:
   - **Treatment Variable**: A binary variable named `treated` was created to identify Starbucks (SBUX) as the treated group. This variable was set to 1 for Starbucks and 0 for other stocks.
   - **Post-Event Variable**: Another binary variable, `post`, was introduced to differentiate between pre-event and post-event periods. This variable was set to 1 for dates on or after the event date and 0 otherwise.
   - **Interaction Term**: An interaction term, `treated_post`, was calculated by multiplying the `treated` and `post` variables. This term captures the combined effect of being in the treated group and the post-event period.

3. **Define and Fit Regression Model**:
   - An Ordinary Least Squares (OLS) regression model was fitted with stock returns as the dependent variable and the treatment, post-event, and interaction terms as independent variables.
   - The regression results indicated a significant positive coefficient for the `treated_post` variable, suggesting a notable impact of the CEO announcement on Starbucks' stock returns.
   - <img width="592" alt="image" src="https://github.com/user-attachments/assets/961c7985-4ee4-442c-8652-b043790255a8">

4. **Estimate Impact**:
   - The analysis estimated an increase in Starbucks' market value of approximately $20,476,849,918 due to the CEO announcement. This reflects the positive market reaction to the leadership change.

5. **Placebo Testing**:
   - Placebo tests were performed by applying the DiD analysis to various pre-event dates to ensure that the observed effect was not due to random chance.
   - The root mean square error (RMSE) from these placebo tests was 0.022, confirming that the observed impact was robust and not likely attributable to random fluctuations in stock prices.
   - ![image](https://github.com/user-attachments/assets/9a991d29-e0c2-47b9-8727-9c4e80baa859)


### Interpretation

The Difference-in-Differences analysis revealed a significant positive effect of the CEO announcement on Starbucks' stock price, with an estimated gain of around $20 billion. This suggests that the market reacted favorably to the leadership change, resulting in a substantial increase in the company's market value. The placebo tests validated the robustness of these findings, demonstrating that the observed effect was genuine and not merely a result of random market variations.

This analysis highlights the effectiveness of the DiD method in quantifying the impact of major corporate events and provides valuable insights into how such announcements can influence stock prices.

View my notebook with detailed steps here:
[causal-inference.ipynb](causal-inference.ipynb)

## Conclusion

### Project Summary

In this project, we explored the causal impact of Starbucks' CEO announcement on its stock price using two statistical methods: Event Study Analysis and Difference-in-Differences (DiD). The goal was to quantify how the announcement, made on August 13, 2024, affected Starbucks' market value, while controlling for the influence of competing stocks in the market.

### Findings

1. **Event Study Analysis**:
   - The Event Study Analysis focused on assessing the stock returns of Starbucks and its competitors over a 120-day window surrounding the CEO announcement.
   - The analysis estimated a significant positive impact on Starbucks' stock price, with an estimated gain of approximately $19.96 billion.
   - Placebo tests conducted to validate these findings produced an RMSE of 0.025, suggesting that while the impact was notable, the robustness of the result could be further examined.

2. **Difference-in-Differences (DiD)**:
   - The DiD approach involved comparing the changes in Starbucks' stock returns before and after the CEO announcement with those of its competitors.
   - The DiD analysis revealed a significant positive effect of the announcement, with an estimated increase in market value of around $20.48 billion.
   - Placebo tests conducted as part of the DiD approach yielded a lower RMSE of 0.022, indicating that the observed impact was robust and less likely to be attributed to random fluctuations.

### Model Comparison and Best Choice

Among the two models employed, the Difference-in-Differences (DiD) method emerged as the more reliable approach for this analysis. The DiD model's lower RMSE of 0.022 compared to the Event Study Analysis's RMSE of 0.025 suggests that the DiD approach provides a more accurate and stable estimate of the causal effect of the CEO announcement on Starbucks' stock price.

In conclusion, the Difference-in-Differences method was preferred for this project due to its better performance in terms of model accuracy and robustness. The analysis confirmed a substantial positive impact on Starbucks' market value, reflecting a favorable investor reaction to the leadership change.
