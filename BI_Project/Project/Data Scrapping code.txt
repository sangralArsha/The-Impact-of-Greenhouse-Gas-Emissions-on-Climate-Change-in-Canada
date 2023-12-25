import requests
from bs4 import BeautifulSoup

url="https://dd.weather.gc.ca/climate/observations/monthly/csv/"



def href_link(url):
    href_link = []

    response = requests.get(url)

    if response.status_code ==200:
        soup = BeautifulSoup(response.text, 'html.parser')
        links = soup.find_all('a', href=True)

        # Print the href content of each link
        for link in links:
            href_content = link['href'] 
            if href_content not in ['?C=N;O=D','?C=M;O=A','?C=S;O=A','?C=D;O=A','/climate/observations/monthly/']:
                href_link.append(href_content)

        return(href_link)
    else:
        return("failed response") 


links = href_link(url) 

def csv_file(url):
    
        csv_url = url+"ON/"
        response = requests.get(csv_url)
        if response.status_code == 200:
            soup = BeautifulSoup(response.text, 'html.parser')
        
            def filter_links(tag):
                return tag.name == 'a' and tag.has_attr('href') and 'climate_monthly_ON_6012199_2011' in tag['href']
            
            selected_links = soup.find_all(filter_links)
            
            for link in selected_links:
                print(link['href'])
                csv_response = requests.get(csv_url+link['href'])
            
                if csv_response.status_code == 200:
                    local_file_path = "downloaded_temp.csv"
                    with open(local_file_path, 'ab') as csv_file:
                        csv_file.write(csv_response.content)

                else:
                    print(f"Failed to download the CSV file. Status code: {csv_response.status_code}")         







csv_file(url)            
