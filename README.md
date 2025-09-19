# stack-spy

Stack Spy is a Python-based tool for analyzing and discovering the technology stack of websites. It asynchronously scans a list of URLs and identifies technologies based on patterns in HTML content, headers, cookies, and specific routes. I made it as a free and open alternative to wappalyzer.

## Installation
First, clone the Github repository:
```bash
git clone https://github.com/EssEnemiGz/stack-spy.git
cd stack-spy```

```bash
python3 -m venv venv
source venv/bin/activate
pip install .
```

This will install the necessary `aiohttp` dependency as well.

## Usage

To use stack-spy, you need to configure the list of target URLs. Open the `utils/configurator.py` file and modify the `url_list` with the websites you want to analyze (this will change later, I'm working on CSV, database and JSON alternative for this list):

```python
# utils/configurator.py

# ...
    async def get_routes(self, summoner) -> List:
        url_list = ["https://your-target-url.com", "https://another-target.com"] # Modify this list
        queue = [summoner(url) for url in url_list]
        return queue
```

Once you have configured the target URLs, you can run Stack Spy from the root directory of the project:

```bash
python discover_tech.py
```

The tool will then process the URLs and print the analysis results in JSON format to the console.

## Configuration

The technology detection rules are defined in the `sources.json` file. You can add or modify the rules in this file to customize the technology detection.

Each entry in `sources.json` represents a technology and contains the following fields:

- `html`: A list of strings to search for in the HTML body of a page.
- `routes`: A list of URL paths to check for. The tool will verify if these paths exist on the target server.
- `headers_patterns`: A dictionary of regular expression patterns to match against the response headers.
- `cookies_patterns`: A list of strings to search for in the response cookies.
- `confidence_weights`: (Note: This feature is not yet implemented) A dictionary that will be used to assign a confidence score to the detection based on the evidence found.

Here is an example for "Wordpress":

```json
{
  "Wordpress": {
    "html": ["wp-content", "wp-includes"],
    "routes": ["/wp-json/"],
    "headers_patterns": {
      "x-powered-by":"(wp engine)"
    },
    "cookies_patterns": ["wordpress_logged_in"],
    "confidence_weights": {
      "html": 40,
      "routes": 30,
      "headers": 20,
      "cookies": 10
    }
  }
}
```

## Output

The output of Stack Spy is a JSON array where each object represents the analysis of a single URL. The structure of the output is as follows:

```json
[
    {
        "https://blog.mozilla.org/en": {
            "Wordpress": {
                "html": true,
                "headers": false,
                "cookies": true,
                "routes": true
            },
            "Cloudflare": {
                "html": false,
                "headers": true,
                "cookies": false,
                "routes": false
            }
        }
    }
]
```

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for more details.

