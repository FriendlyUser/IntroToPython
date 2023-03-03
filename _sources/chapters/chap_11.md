---
jupytext:
  formats: md:myst
  text_representation: 
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.11.5
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

# Chapter 10 Python Projects


## Image magick
ImageMagick is a software suite to create, edit, and compose bitmap images. It can read, convert and write images in a variety of formats (over 100) including DPX, EXR, GIF, JPEG, JPEG-2000, PDF, PhotoCD, PNG, Postscript, SVG, and TIFF. ImageMagick is used to translate, flip, mirror, rotate, scale, shear and transform images, adjust image colors, apply various special effects, or draw text, lines, polyglines, ellipses and Bézier curves.

It is used by graphic designers, photographers, scientists and also used for creating thumbnails for websites, creating GIF animations, converting PDF pages to images and so on. ImageMagick can be used from the command-line or can be used as a programming library for software development.


You can call ImageMagick from Python using the `os` module, which provides a way to interface with the underlying operating system.

Here's an example of how to use ImageMagick to convert an image from one format to another:


```python
import os

input_image = 'input.jpg'
output_image = 'output.png'

os.system(f'convert {input_image} {output_image}')
```
In this example, `convert` is the command-line utility provided by ImageMagick to convert images. The `input_image` variable specifies the path to the input image and `output_image` specifies the path to the output image. The `os.system` function is used to run the `convert` command, which converts the input image to the output image.

The convert utility is only available in imagemagick 6.

Note that this method is prone to security vulnerabilities, since it passes the parameters directly to the shell. To avoid these vulnerabilities, you can use the `subprocess` module, which provides a more secure way of calling shell commands. Here's an example of how to use the `subprocess` module to call ImageMagick:


```python
import subprocess

input_image = 'input.jpg'
output_image = 'output.png'

subprocess.run(['convert', input_image, output_image])
```
In this example, the `subprocess.run` function is used to run the `convert` command, and the parameters are passed as a list, rather than as a string. This is a more secure way of calling shell commands, as it avoids shell injection attacks.


You can call ImageMagick from Python using the `os` module, which provides a way to interface with the underlying operating system.

Here's an example of how to use ImageMagick to convert an image from one format to another:


```python
import os

input_image = 'input.jpg'
output_image = 'output.png'

os.system(f'convert {input_image} {output\_image}')`
```
In this example, `convert` is the command-line utility provided by ImageMagick to convert images. The `input_image` variable specifies the path to the input image and `output_image` specifies the path to the output image. The `os.system` function is used to run the `convert` command, which converts the input image to the output image.

Note that this method is prone to security vulnerabilities, since it passes the parameters directly to the shell. To avoid these vulnerabilities, you can use the `subprocess` module, which provides a more secure way of calling shell commands. Here's an example of how to use the `subprocess` module to call ImageMagick:


```python
import subprocess

input_image = 'input.jpg'
output_image = 'output.png'

subprocess.run(['convert', input_image, output_image])`
```
In this example, the `subprocess.run` function is used to run the `convert` command, and the parameters are passed as a list, rather than as a string. This is a more secure way of calling shell commands, as it avoids shell injection attacks.


```python
import os

def main():
    dir_list =  os.listdir('.')
    for full_file_name in dir_list:
        base_name, extension = os.path.splitext(full_file_name)
        if extension == '.pdf': # then .pdf file --> convert to image!
            cmd_str = ' '.join(['convert',
                                '-density 400',
                                full_file_name,
                                '-flatten',
                                base_name + '.png'])
            print(cmd_str)  # echo command to terminal
            os.system(cmd_str)  # execute command

if __name__ == '__main__':
    main()
```

This is a Python script that uses the os module to convert all PDF files in the current directory to PNG images using ImageMagick. The main function lists all the files in the current directory using os.listdir, and then loops through each file name in dir_list. If the file extension is '.pdf', the script splits the file name into its base name and extension using os.path.splitext, and then creates a command string using cmd_str that calls ImageMagick's convert utility with the specified options.

The options used in this script are:

- density 400: sets the density of the output image, which affects its resolution.
- flatten: combines all the layers of the PDF into a single image.

The os.system function is then used to execute the command string, which converts the PDF file to a PNG image. The script prints the command string to the terminal to show what it's doing, and the result is a set of PNG images, one for each PDF file in the directory.

ImageMagick is a versatile and powerful software suite that has many uses, including:

1. Image Conversion: ImageMagick can be used to convert images from one format to another, making it easier to work with different image formats.
2. Image Editing: ImageMagick can be used to perform basic image editing operations, such as cropping, resizing, rotating, and flipping images.
3. Image Compression: ImageMagick can be used to compress images, reducing their file size while maintaining image quality.
4. Image Manipulation: ImageMagick can be used to manipulate images in various ways, such as applying special effects, adding text and shapes, and blending multiple images together.
5. Thumbnail Creation: ImageMagick can be used to create thumbnails for websites, making it easier to manage large numbers of images.
6. Image Animation: ImageMagick can be used to create GIF animations from a sequence of images.
7. PDF Conversion: ImageMagick can be used to convert PDF pages to images, making it easier to work with PDF documents.
8. Image Processing: ImageMagick can be used for image processing tasks, such as adjusting image colors and removing noise from images.
9. Scientific Visualization: ImageMagick can be used in scientific visualization, such as for rendering 3D models or visualizing data.

These are just a few examples of the many uses of ImageMagick. It is a highly versatile software suite that can be used in many different ways to work with images.


### References
- https://github.com/FriendlyUser/ELEC370/blob/master/Assignments/A1/html/pdfToPNGMagick.py


## Chat gpt blog generation
ChatGPT is a large language model that has been trained using a technique called unsupervised learning, in which the model learns to generate text by being fed a large dataset of text. It is designed to generate human-like responses to prompts, and can be used for a variety of natural language processing tasks, such as language translation, text summarization, and question answering.


```python 
import os
import re
import argparse
from pyChatGPT import ChatGPT
# import yaml
from yaml import load, dump, Loader
import io
import warnings
from PIL import Image
from stability_sdk import client
import stability_sdk.interfaces.gooseai.generation.generation_pb2 as generation
# Our Host URL should not be prepended with "https" nor should it have a trailing slash.
os.environ['STABILITY_HOST'] = 'grpc.stability.ai:443'
os.environ["STABILITY_KEY"] = os.getenv("STABILITY_KEY")

def generate_image(cfg:dict)-> None:
    prompt = cfg['imageArgs']['prompt']
    stability_api = client.StabilityInference(
        key=os.environ['STABILITY_KEY'], # API Key reference.
        verbose=True, # Print debug messages.
        engine="stable-diffusion-v1-5", # Set the engine to use for generation. 
        # Available engines: stable-diffusion-v1 stable-diffusion-v1-5 stable-diffusion-512-v2-0 stable-diffusion-768-v2-0 
        # stable-diffusion-512-v2-1 stable-diffusion-768-v2-1 stable-inpainting-v1-0 stable-inpainting-512-v2-0
    )
    answers = stability_api.generate(
        prompt=prompt
    )
    for resp in answers:
        for artifact in resp.artifacts:
            if artifact.finish_reason == generation.FILTER:
                warnings.warn(
                    "Your request activated the API's safety filters and could not be processed."
                    "Please modify the prompt and try again.")
            if artifact.type == generation.ARTIFACT_IMAGE:
                img = Image.open(io.BytesIO(artifact.binary))
                # images
                img.save("images" + "/" + str(artifact.seed)+ ".png") # Save our generated images with their seed number as the filename.

def generate_frontmatter(cfg: dict)-> None:
    # open output file
    output_file = cfg['outputFile']
    with open(output_file, 'w') as f:
        # write frontmatter
        f.write('---\n')
        # get frontmatter
        frontmatter = cfg['frontMatter']
        for key, value in frontmatter.items():
            f.write(f'{key}: {value}\n')
        f.write('---\n')
        # write body
    pass

def use_programming_language(cfg: dict, section_text: str)-> None:
    # get programming language
    programming_language = cfg['programmingLanguage']
    # use regex to find plain ``` and replace with ```programming_language
    type_start_line = None
    type_end_line = None
    # replace with code blocks with programming language
    # split lines by \n
    modified_lines = []
    lines = section_text.split('\n')
    for i, line in enumerate(lines):
        if line == '```':
            if type_start_line is None:
                type_start_line = i
                # append modified line
                modified_lines.append(f'```{programming_language}')
            else:
                # odd number of ``` so we are ending a code block
                type_start_line = None
                # append modified line
                modified_lines.append('```')
        else:
            # adjust line if type_start_line is not None
            # remove ` at the beginning of the line and the end of the line
            if type_start_line is not None:
                # check for ` character at the beginning of the line
                if line[0] == '`':
                    line = line[1:]
                # check for ` character at the end of the line
                if line[-1] == '`':
                    line = line[:-1]
            modified_lines.append(line)

    return "\n".join(modified_lines)

def generate_body(cfg: dict)-> None:
    # read CHATGPT_TOKEN from os
    CHATGPT_SESSION_TOKEN = os.getenv("CHATGPT_TOKEN")
    # print(CHATGPT_SESSION_TOKEN)
    session_token = CHATGPT_SESSION_TOKEN  # `__Secure-next-auth.session-token` cookie from https://chat.openai.com/chat
    api = ChatGPT(session_token)  # auth with session token
    output_file = cfg['outputFile']
    sections = cfg['sections']
    with open(output_file, 'a') as f:
        for section in sections:
            # check if str or dict
            if isinstance(section, str):
                resp = api.send_message(section)
                clean_message = use_programming_language(cfg, resp['message'])
            else:
                # assume dict
                # type and src from dict
                input_type = section['type']
                src = section['src']
                if input_type == 'file':
                    # read src from file
                    with open(src, 'r') as src_file:
                        src_text = src_file.read()
                    resp = api.send_message(src_text)
                    clean_message = resp['message']
                    print(clean_message)
                    # write original text
                    programming_language = cfg['programmingLanguage']
                    f.write(f"```{programming_language} \n {src_text} \n ```\n")
                    f.write('\n')
            f.write(clean_message)
            f.write('\n')

if __name__ == "__main__":
    parser = argparse.ArgumentParser()
    parser.add_argument('--file', type=str, default='posts/chatgpt_blog_generation.yml')
    args = parser.parse_args()
    # valid files exist
    # argparse for file eventually
    with open(args.file, 'r') as f:
        cfg = load(f, Loader=Loader)
    # if file exists skip
    # if not run these functions
    if os.path.exists(cfg['outputFile']):
        print('file exists')
        exit(0)
    # if genImage is true, then makeImage
    if cfg['imageArgs']:
        generate_image(cfg)
        pass

    generate_frontmatter(cfg)
    generate_body(cfg) 
 ```

This is a Python script that uses the ChatGPT API, the pyChatGPT library, and the stability\_sdk to generate text and images. The script takes in a configuration file in YAML format, which specifies the input text, the output file, and various settings such as the programming language to use for code blocks and the prompt for the image generation.
It uses the ChatGPT API to generate text in response to prompts specified in the configuration file. The script also uses the stability\_sdk to generate images based on the prompt specified in the configuration file. The script also uses the Python Imaging Library (PIL) to save the generated images to a directory. Additionally, it processes the generated text, using regular expressions to replace plain backticks with code blocks in the specified programming language.


There are several ways to monetize a blog, including the following:

1. Advertising: You can place ads on your blog and earn money through pay-per-click or pay-per-impression programs such as Google AdSense.
2. Affiliate marketing: You can earn money by promoting other people's products or services and receiving a commission for every sale made through your unique affiliate link.
3. Sponsored posts: You can write posts sponsored by a brand or company and receive payment for the promotion.
4. Selling products or services: You can sell your own products or services, such as e-books, courses, or consulting services, directly to your readers.
5. Crowdfunding: You can use platforms like Patreon or Kickstarter to raise money from your readers to support your blog or other projects.
6. Subscription models: You can offer exclusive content, access to a community or other perks for a recurring fee.

It's important to note that monetizing your blog requires consistent, high-quality content, and a sizable audience. It may take some time and effort to grow your blog and monetize it successfully.


Using AI to generate images can be beneficial in a variety of ways. Some reasons include:

1. Automation: AI-generated images can be produced quickly and at scale, reducing the time and resources required to create images manually.
2. Variation: AI-generated images can be easily customized to produce a wide range of variations, allowing for greater flexibility in image design.
3. Consistency: AI-generated images can be designed to maintain a consistent style or theme, making it easier to maintain a cohesive visual identity across a brand or product.
4. Cost-effectiveness: AI-generated images can be less expensive than hiring a professional photographer or graphic designer, making them more accessible to businesses with limited budgets.
5. Creative possibilities: AI-generated images can be used in creative ways, such as creating surreal or abstract imagery that would be difficult to achieve through traditional means.
6. Personalization: AI-generated images can be used to personalize content for targeted audience, resulting in higher engagement and conversion rates.
7. Efficiency: AI-generated images can be used to generate high-quality images for use in various applications, such as product advertising, web design, and mobile apps.

Using AI to generate images can be beneficial in a variety of ways. Some reasons include:

1. Automation: AI-generated images can be produced quickly and at scale, reducing the time and resources required to create images manually.
2. Variation: AI-generated images can be easily customized to produce a wide range of variations, allowing for greater flexibility in image design.
3. Consistency: AI-generated images can be designed to maintain a consistent style or theme, making it easier to maintain a cohesive visual identity across a brand or product.
4. Cost-effectiveness: AI-generated images can be less expensive than hiring a professional photographer or graphic designer, making them more accessible to businesses with limited budgets.
5. Creative possibilities: AI-generated images can be used in creative ways, such as creating surreal or abstract imagery that would be difficult to achieve through traditional means.
6. Personalization: AI-generated images can be used to personalize content for targeted audience, resulting in higher engagement and conversion rates.
7. Efficiency: AI-generated images can be used to generate high-quality images for use in various applications, such as product advertising, web design, and mobile apps.

AI has the potential to change blog writing in the future by making the process more efficient and personalized. For example, AI-powered writing assistants can help writers generate content faster by suggesting topics, outlining structure, and providing research and data to support their ideas. Additionally, AI-powered content generation can personalize blogs by analyzing audience data and tailoring the content to their interests and preferences. It can also generate images, infographics and videos to supplement the written content. However, it's important to note that AI-generated content should be carefully evaluated for accuracy and bias, and should be used in conjunction with human editing and oversight to ensure high-quality and ethical results.

### References
- https://github.com/FriendlyUser/chatgpt-blog-generator


## Investor document downloader

CSE exchange refers to the Canadian Securities Exchange, which is a stock exchange located in Canada. The CSE is a fully electronic exchange that provides a marketplace for the trading of securities listed on its platform. It offers a range of listing options, including equity, debt, and structured products, and operates with a focus on serving emerging and growth companies. The exchange aims to provide an efficient, transparent, and accessible marketplace for issuers and investors, and is regulated by the Ontario Securities Commission.


Downloading reports from the CSE exchange can provide valuable information for investors, traders, and other market participants. The reports contain data on market activity, financial performance, and other key metrics for the companies listed on the exchange. By accessing these reports, you can gain insight into the financial health and performance of the companies, which can help inform your investment decisions.

For example, the CSE provides annual and quarterly financial statements, press releases, and other disclosures for companies listed on the exchange. By downloading and reviewing these reports, you can get a better understanding of a company's financial performance, including revenue, expenses, profits, and growth trends. This information can help you make more informed decisions about whether to buy, sell, or hold a company's stock.

Additionally, downloading CSE reports can provide you with insights into the overall health of the Canadian securities market, as well as emerging trends and opportunities. This information can be especially useful for traders and investors looking to make strategic decisions about their portfolios.

In summary, downloading reports from the CSE exchange can provide valuable information for market participants and


```python 
 import pandas as pd
import time
import os
import requests
import json
from cad_tickers.exchanges.cse import get_recent_docs_from_url
from extract_doc import mk_dir, handle_logic
from io import BytesIO, StringIO
"""
1. Get the list of documents from the CSE website
2. For each document, get the document url
3. If the document url is not in the csv file, add it to the csv file
4. For each document url, download the document and add it to the docs folder
5. For each document url, make a discord request with the document url
6. For each document url, make a discord request with the document summary
"""


def fig_to_buffer(fig):
  """ returns a matplotlib figure as a buffer
  """
  buf = BytesIO()
  fig.savefig(buf, format='png')
  buf.seek(0)
  imgdata = buf.read()
  return imgdata

def make_discord_request(content, embeds = [], filename = None, file = None):
    url = os.getenv("DISCORD_WEBHOOK")
    if url == None:
        print('DISCORD_WEBHOOK Missing')
        pass
    data = {}
    data["content"] = content
    files = {'file': (filename, file, 'application/pdf')}
    if filename != None and file != None:
        resp = requests.post(
            url, data=data, files=files
        )
    elif len(embeds) != 0:
        data["embeds"] = embeds
        resp = requests.post(
            url, data=json.dumps(data), headers={"Content-Type": "application/json"}
        )
    print(resp) 
 ```

This code is a Python script that downloads documents from the Canadian Securities Exchange (CSE) website and makes a Discord request with the document URL and summary. Here's a brief overview of what it does:

1. It retrieves a list of documents from the CSE website using the `get_recent_docs_from_url` function.
2. For each document, it retrieves the document URL.
3. If the document URL is not already in a CSV file, it adds it to the CSV file.
4. It downloads the document and saves it to a directory called `docs`.
5. For each document URL, it makes a Discord request with the document URL.
6. For each document URL, it makes another Discord request with the document summary.

The Discord request is sent using the `requests` library. The `make_discord_request` function takes in several arguments, including the content, embeds, filename, and file data. If the filename and file data are provided, the function makes a request with the file attached. If the embeds argument is provided, the function makes a request with the embeds data. The URL for the Discord webhook is read from an environment variable `DISCORD_WEBHOOK`.


```python 
 
stockList = ["PKK", "IDK", "ADDC", "VPH", "VST", "ACT"]
def get_cse_tickers_data():
    url = "https://github.com/FriendlyUser/cad_tickers_list/blob/main/static/latest/cse.csv?raw=true"
    r = requests.get(url, allow_redirects=True)
    s = r.content
    return pd.read_csv(StringIO(s.decode('utf-8')))

stock_df = get_cse_tickers_data()
stock_rows = stock_df.loc[stock_df['Symbol'].isin(stockList)]
stock_rows.loc[:, 'stock'] = stock_rows['Symbol']
stock_rows.loc[:, 'url'] = stock_rows['urls']
stockUrls = stock_rows.to_dict('records')
csv_file = "docs.csv"
if os.path.isfile(csv_file):
    # read from csv
    df = pd.read_csv(csv_file)
else:
    # make new df
    df = pd.DataFrame(columns=["stock", "url", "docUrl"])
 
 ```

This code is retrieving data for a list of stocks (`stockList`) from the Canadian Securities Exchange (CSE). It uses the `get_cse_tickers_data` function to retrieve the data from a CSV file hosted on GitHub, which contains information about CSE stocks, such as their symbol, name, and URLs. The function uses the `requests` library to make a GET request to the URL of the CSV file, and the returned content is then converted into a Pandas DataFrame using `pd.read_csv`.

Next, the code filters the data for only the stocks in the `stockList` by using the `loc` method on the DataFrame and checking if the value in the `Symbol` column is in the `stockList` using the `isin` method. The filtered data is then saved in the `stock_rows` DataFrame.

The code then creates two new columns in the `stock_rows` DataFrame, one named `stock` and the other named `url`, which contain the values from the `Symbol` and `urls` columns, respectively. The filtered data is then converted into a list of dictionaries (`stockUrls`) using the `to_dict` method.

Finally, the code checks if there is an existing CSV file named `docs.csv` in the current directory. If there is, it reads the data into a DataFrame using `pd.


```python 
 for stock in stockUrls:
    stockName = stock.get("stock")
    stockUrl = stock.get("url")
    urls = []
    try:
        urls = get_recent_docs_from_url(stockUrl)
    except Exception as e:
        pass
    
    for docUrl in urls:
        # skip malformed relative urls
        if docUrl[0] == '/':
            continue
        # add each element to list
        exists = docUrl in df["docUrl"].tolist()
        if exists == False:
            print(f"Adding {stockName}: {docUrl}")
            df.loc[len(df)] = [stockName, stockUrl, docUrl]
            # wrap in todo
            stock_doc_dir = f"docs/{stockName}"
            mk_dir(stock_doc_dir)
            stock_doc_file_path = docUrl.split("/")[-1]
            pdf_file_name = f"{stock_doc_dir}/{stock_doc_file_path}.pdf"
            companyName = stock.get("Company").\
                replace('Inc.', '').\
                replace('Pharma', '').\
                strip()
            dataDict = {
                "url": docUrl,
                "path": pdf_file_name,
                "company_name": companyName
            }
            result_obj = handle_logic(dataDict)
            file_contents = result_obj.get("contents")
            summary = "N/A"
            try:
                summary = result_obj.get("summary")[:1980]
            except TypeError as e:
                pass
            embeds = [
                {
                    "title": stockName,
                    "url": docUrl,
                    "description": summary
                }
            ]
            if file_contents != None:
                make_discord_request(f"*{stockName}*: \n {docUrl}", embeds, pdf_file_name, file_contents)
                time.sleep(2)
            else:
                make_discord_request(f"*{stockName}*: \n {docUrl}", embeds)
                time.sleep(1)
            pass

df = df.drop_duplicates()
df = df.sort_values(by=['stock'])
df.to_csv(csv_file, index=False) 
 ```

This code is a script that downloads and summarizes recent documents from a list of stock symbols. The stock symbols are stored in the list `stockList`, and the data for these symbols is obtained by calling the `get_cse_tickers_data` function, which retrieves data from a CSV file stored on GitHub.

The code then loops through each of the stocks in `stockList`, and for each stock, it retrieves a list of recent documents by calling the `get_recent_docs_from_url` function and passing it the URL for the stock.

For each document, the code checks if the document URL already exists in the DataFrame `df`. If the URL does not exist, the code adds the document information (i.e., stock name, URL, and a summary) to `df` and makes a Discord request to post the information to a Discord channel. The information posted to the Discord channel includes the stock name, document URL, and a summary of the document's contents. If the contents of the document are available, they are also included in the Discord request as an attachment.

After all the documents have been processed, the code removes any duplicates in `df` and sorts the entries by the stock name. Finally, the updated `df` is saved to a CSV file.

## References
- https://github.com/dli-invest/cse_file_downloader/blob/main/scrap_cse_releases.py
