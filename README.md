import requests
from bs4 import BeautifulSoup


url = 'https://hprera.nic.in/PublicDashboard'


response = requests.get(url, verify=False)
soup = BeautifulSoup(response.content, 'html.parser')
projects_section = soup.find('div', {'id': 'registered-projects'})


if projects_section:

    projects = projects_section.find_all('div', class_='project', limit=6)


    project_details = []


    for project in projects:
        project_link = project.find('a', {'class': 'rera-number'})['href']
        project_url = f"https://hprera.nic.in{project_link}"


        project_response = requests.get(project_url, verify=False)
        project_soup = BeautifulSoup(project_response.content, 'html.parser')


        gstin = project_soup.find('span', {'id': 'gstin'})
        pan = project_soup.find('span', {'id': 'pan'})
        name = project_soup.find('span', {'id': 'name'})
        address = project_soup.find('span', {'id': 'address'})


        if gstin and pan and name and address:
            gstin_text = gstin.text.strip()
            pan_text = pan.text.strip()
            name_text = name.text.strip()
            address_text = address.text.strip()


            project_details.append({
                'GSTIN No': gstin_text,
                'PAN No': pan_text,
                'Name': name_text,
                'Permanent Address': address_text
            })
        else:
            print(f"Missing details for project at {project_url}")


        import time
        time.sleep(1)

    # Print the project details
    for i, details in enumerate(project_details, start=1):
        print(f"Project {i}:")
        for key, value in details.items():
            print(f"{key}: {value}")
        print()
else:
    print("No projects found.")
