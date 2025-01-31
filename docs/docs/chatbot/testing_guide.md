# Testing Guide

Author: `Cansu Moran`

This project includes **unit tests** to ensure the reliability of our data uploaders and parsers. Below is an overview of how the testing framework is set up and how you can run the tests.

---

## **Folder Structure**
- All test files are located under the `tests` folder.
- Each test file corresponds to a specific module or functionality, ensuring thorough coverage of the codebase.

---

## **Running the Tests**
To run all the tests together, use the following command:

```bash
python -m unittest discover tests
```

This will discover and execute all test cases within the `tests` folder.

---

## **Pre-commit Hook for Testing**
We have configured a **pre-commit hook** to ensure all tests pass before committing changes. This helps maintain code quality and prevents broken code from being committed to the repository.  

To set up the pre-commit hook:
1. Make sure you have `pre-commit` installed. Install it using:
   ```bash
   pip install pre-commit
   ```
2. Install the hook by running:
   ```bash
   pre-commit install
   ```

Now, every time you try to commit, the pre-commit hook will automatically run the tests. If any test fails, the commit will be blocked.

---

## **GitHub Test Check**
In addition to the pre-commit hook, we also enforce testing at the **pull request (PR)** level.  
When a PR is submitted, GitHub Actions will automatically run the tests.  

- If all tests pass, the PR will be mergeable.
- If any test fails, the PR will be blocked until the issues are resolved.

---

## **Example Test**
Here’s an example of a unit test for a data uploader (`process_pdc_csv_data` and `upload_pdc_data`):

```python
import unittest
from unittest.mock import MagicMock, patch

from ..data_uploaders.db_upload_country_pdc_data import (
    process_pdc_csv_data,
    upload_pdc_data,
)


class TestPDCDataUploader(unittest.TestCase):
    @patch("src.data_uploaders.db_upload_country_pdc_data.read_csv_data")
    def test_process_pdc_csv_data(self, mock_get_country_and_city):
        """
        Test process_pdc_csv_data with mock data.
        """
        mock_get_country_and_city.return_value = ("Afghanistan", None)
        mock_data = [
            {
                "id": "1230",
                "type": "CIVILUNREST",
                "severityId": "INFORMATION",
                "hazardName": "Civil Unrest",
                "latitude": "34.5553",
                "longitude": "69.2075",
                "createDate": "2024-10-27 11:38:00",
                "lastUpdate": "2024-10-27 11:46:25",
            }
        ]
        expected_output = [
            {
                "document_name": "Afghanistan_pdc",
                "type": "CIVILUNREST",
                "country_name": "Afghanistan",
                "occurrences": [
                    {
                        "id": "1230",
                        "type": "CIVILUNREST",
                        "severity_id": "INFORMATION",
                        "hazard_name": "Civil Unrest",
                        "country": "AF",
                        "create_date": "2024-10-27 11:38:00",
                        "last_update": "2024-10-27 11:46:25",
                    }
                ],
                "data_labels": ["pdc_data"],
                "data_description": (
                    "Information about the CIVILUNREST events that occurred in Afghanistan. "
                    "It includes the event's id, type, severity, hazard name, country name,"
                    "create date, and last update."
                ),
            }
        ]
        result = process_pdc_csv_data("Afghanistan", mock_data)
        self.assertEqual(result, expected_output)
```

This test:
- Mocks dependencies using `@patch` from `unittest.mock`.
- Simulates various scenarios (e.g., mock data input).
- Asserts expected outcomes.

For additional examples, check the `tests` folder in the repository.

---

## **Best Practices**
- Write clear and concise test cases for new modules or features.
- Use mocking (`unittest.mock`) for external dependencies, such as database connections or API calls.
- Run the tests locally before committing code to ensure everything works as expected.