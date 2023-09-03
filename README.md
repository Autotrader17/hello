import requests
import time

# Angel Broking API credentials
API_KEY = 'YOUR_API_KEY'
API_SECRET = 'YOUR_API_SECRET'
API_BASE_URL = 'https://smartapi.angelbroking.com'

# Create headers with API key
headers = {
    'X-User-Id': API_KEY,
    'X-Auth-Token': API_SECRET
}

# Fetch historical data
symbol = 'RELIANCE'
interval = '15minute'  # Change the interval as needed (15minute, 1hour, 1day, etc.)
endpoint = f'{API_BASE_URL}/chart/{symbol}/{interval}'
response = requests.get(endpoint, headers=headers)
data = response.json()

# Extract closing prices from historical data
closing_prices = [candle['c'] for candle in data['data']]

# Calculate moving averages
short_period = 10  # Choose your short moving average period
long_period = 50   # Choose your long moving average period

def calculate_sma(data, period):
    sma = []
    for i in range(len(data) - period + 1):
        sma.append(sum(data[i:i+period]) / period)
    return sma

short_sma = calculate_sma(closing_prices, short_period)
long_sma = calculate_sma(closing_prices, long_period)

# Implement the strategy
for i in range(long_period, len(closing_prices)):
    if short_sma[i - short_period] > long_sma[i - long_period] and short_sma[i] < long_sma[i]:
        # This is a "buy" signal
        print(f"Buy signal at index {i}")
        # Place your trading order here

    # Sleep for a while before checking the next data point
    time.sleep(1)
