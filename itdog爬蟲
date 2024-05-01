import sys
import time
import logging
import requests
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.common.exceptions import NoSuchElementException

# Setup logging
logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')

# Browser setup
chrome_options = Options()
chrome_options.add_argument("--disable-gpu")
chrome_options.add_argument("--no-sandbox")
chrome_options.add_argument("--headless")
chrome_options.add_argument("--window-size=1920,1080")

# Specify the ChromeDriver path
chrome_driver_path = '/root/cdn/chromedriver'
driver = webdriver.Chrome(executable_path=chrome_driver_path, options=chrome_options)
driver.get('https://www.itdog.cn/http/')

try:
    # Wait for the specific button and click it before filling the input
    specific_button = WebDriverWait(driver, 10).until(
        EC.element_to_be_clickable((By.XPATH, '/html/body/div[4]/div/div/div/div/div/div[3]/div/div[3]/div[1]/label[5]/span'))
    )
    specific_button.click()

    # Explicit wait for the input field to be ready
    input_element = WebDriverWait(driver, 10).until(
        EC.presence_of_element_located((By.ID, 'host'))
    )
    
    if len(sys.argv) > 1:
        domain = sys.argv[1]
        comment = sys.argv[2] if len(sys.argv) > 2 else ''  # Get the comment if provided
    else:
        print("Error: No domain provided.")
        sys.exit(1)

    input_element.send_keys(domain)

    # Wait for the button and click it
    button_element = WebDriverWait(driver, 10).until(
        EC.element_to_be_clickable((By.XPATH, '//button[@onclick="check_form(\'fast\')"]'))
    )
    button_element.click()

    # Wait 10 seconds after clicking the button
    time.sleep(10)

    # Try to locate the element containing the desired value
    try:
        value_element = driver.find_element(By.XPATH, '/html/body/div[4]/div/div/div/div/div/div[7]/div/div/div[1]/span[1]/span[2]')
        value_text = value_element.text.strip()
        value = int(value_text) if value_text.isdigit() else 0
    except NoSuchElementException:
        logging.info("The specified element was not found on the page.")
        value = 0

    logging.info(f'Retrieved value: {value}')

    # Check if the value meets the threshold and send a Telegram message
    if value >= 2:
        bot_token = "bot_token"
        chat_id = "chat_id"
        message = f"域名{domain}\n{comment}\n目前經ITDOG網站測試異常5個以上地區"
        url = f"https://api.telegram.org/bot{bot_token}/sendMessage"
        payload = {
            'chat_id': chat_id,
            'text': message
        }
        response = requests.post(url, data=payload)
        logging.info(f"Telegram message sent with response: {response.text}")

finally:
    driver.quit()  # Make sure to close the browser
