import time
import pytest
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.chrome.service import Service
from webdriver_manager.chrome import ChromeDriverManager

# Setup the WebDriver
@pytest.fixture(scope="module")
def setup_browser():
    # Setup Chrome driver
    options = webdriver.ChromeOptions()
    driver = webdriver.Chrome(service=Service(ChromeDriverManager().install()), options=options)
    yield driver
    driver.quit()

# Test case for search functionality on Selenium Playground Table Search Demo
def test_search_functionality(setup_browser):
    driver = setup_browser

    # Navigate to the Selenium Playground Table Search Demo page
    driver.get("https://www.seleniumeasy.com/test/table-search-filter-demo.html")
    
    # Wait for the page to load
    time.sleep(2)

    # Locate the search box
    search_box = driver.find_element(By.ID, "task-table-filter")

    # Clear any pre-existing text and enter 'New York' into the search box
    search_box.clear()
    search_box.send_keys("New York")
    
    # Wait for search results to load
    time.sleep(2)

    # Get all rows in the table after search
    table_rows = driver.find_elements(By.XPATH, "//table[@id='task-table']//tr")

    # Validate there are exactly 5 rows shown after filtering
    assert len(table_rows) == 6, f"Expected 5 entries, but got {len(table_rows)-1} entries"  # Includes header row

    # Validate that all rows contain 'New York'
    for row in table_rows[1:]:  # Skipping the header row
        cell_text = row.find_element(By.XPATH, ".//td[2]").text  # Check second column for location
        assert "New York" in cell_text, f"Expected 'New York' but found {cell_text}"

    # Verify total number of rows after search should be 5 (excluding header)
    total_rows = len(driver.find_elements(By.XPATH, "//table[@id='task-table']//tr"))
    assert total_rows == 6, f"Expected total 5 results, but got {total_rows - 1} entries"

    # Confirm success message on the test output
    print("Search functionality test passed successfully!")
