conftest.py
import pytest
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.chrome.options import Options

@pytest.fixture(scope="class")
def setup(request):
    chrome_options = Options()
    chrome_options.add_argument("--start-maximized")
    service = Service()
    driver = webdriver.Chrome(service=service, options=chrome_options)
    driver.implicitly_wait(5)
    request.cls.driver = driver
    yield
    driver.quit()

   home_page.py
   from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

class HomePage:
    def _init_(self, driver):
        self.driver = driver
        self.logout_button = (By.ID, "logoutBtn")

    def is_logout_visible(self):
        try:
            WebDriverWait(self.driver, 10).until(
                EC.visibility_of_element_located(self.logout_button)
            )
            return True
        except:
            return False

    def click_logout(self):

    login_page.py
    class LoginPage:
    def _init_(self, driver):
        self.driver = driver
        self.username_input = (By.ID, "username")
        self.password_input = (By.ID, "password")
        self.login_button = (By.ID, "loginBtn")

    def open_portal(self, url):
        self.driver.get(url)

    def enter_username(self, username):
        WebDriverWait(self.driver, 10).until(
            EC.visibility_of_element_located(self.username_input)
        ).send_keys(username)

    def enter_password(self, password):
        WebDriverWait(self.driver, 10).until(
            EC.visibility_of_element_located(self.password_input)
        ).send_keys(password)

    def click_login(self):
        WebDriverWait(self.driver, 10).until(
            EC.element_to_be_clickable(self.login_button)
        ).click()

    def get_error_message(self):
        try:
            error = WebDriverWait(self.driver, 5).until(
                EC.visibility_of_element_located((By.CLASS_NAME, "error"))
            )
            return error.text
        except:
            return None

            test_zen-portal.py
            import pytest
from pages.login_page import LoginPage
from pages.home_page import HomePage

@pytest.mark.usefixtures("setup")
class TestZenPortal:

    def test_successful_login(self):
        login = LoginPage(self.driver)
        home = HomePage(self.driver)

        login.open_portal("https://your-zen-portal-url.com")
        login.enter_username("valid_user")
        login.enter_password("valid_password")
        login.click_login()

        assert home.is_logout_visible(), "Login Failed - Logout not visible"

        home.click_logout()
        print("Successful Login and Logout verified")

    def test_unsuccessful_login(self):
        login = LoginPage(self.driver)
        login.open_portal("https://your-zen-portal-url.com")
        login.enter_username("wrong_user")
        login.enter_password("wrong_pass")
        login.click_login()

        error_msg = login.get_error_message()
        assert error_msg is not None, "No error message displayed for invalid login"
        print("Unsuccessful login validated")

    def test_input_boxes_visible(self):
        login = LoginPage(self.driver)
        login.open_portal("https://your-zen-portal-url.com")

        assert self.driver.find_element(*login.username_input).is_displayed()
        assert self.driver.find_element(*login.password_input).is_displayed()
        print("Username and Password input boxes validated")

    def test_submit_button_clickable(self):
        login = LoginPage(self.driver)
        login.open_portal("https://your-zen-portal-url.com")

        assert self.driver.find_element(*login.login_button).is_enabled()
        print("Submit button is clickable")
        WebDriverWait(self.driver, 10).until(
            EC.element_to_be_clickable(self.logout_button)
        ).click()
