## Overview

This repository contains the sources of AsyncAPI website:

- It's powered by Next.js,
- It uses Tailwind CSS framework,
- It's build and deployed with Netlify.

## Requirements

Use the following tools to set up the project:

- Node.js v16.0.0+
- npm v8.10.0+

## Run locally

1. Fork the repository by clicking on `Fork` option on top right of the main repository.

2. Open Command Prompt on your local computer.

3. Clone the forked repository by adding your own GitHub username in place of `<username>`.
   For multiple contributions it is recommended to have proper configuration of forked repo.

4. Navigate to the website directory.

5. Install all website dependencies. 

6. Run the website locally.

7. Access the live development server at [localhost:3000](http://localhost:3000).


## Compose new blog post

To bootstrap a new post, run this command:

```bash
    npm run write:blog
```

Follow the interactive prompt to generate a post with pre-filled front matter.

### Spin up Gitpod codespace

In order to prepare and spin up a Gitpod dev environment for our project, we configured our workspace through a .gitpod.yml file.

### Build

To build a production-ready website, run the following command:

```bash
npm run build
```

Generated files of the website go to the `.next` folder.

### Run locally using Docker

#### Prerequisites:

After cloning repository to your local, perform the following steps from the root of the repository.

#### Steps:
1. Build the Docker image:
    ```bash 
    docker build -t asyncapi-website .`
    ```
2. Start the container:
    ```bash
    docker run --rm -it -v "$PWD":/async -p 3000:3000 asyncapi-website
    ```

Now you're running AsyncAPI website in a development mode. Container is mapped with your local copy of the website. Whenever you make changes to the code, the website will refresh and changes visible in localhost:3000.

## Updating information about project finance

AsyncAPI Financial Summary page aims to provide transparency and clarity regarding the organization's financial activities. It serves as a platform to showcase how donations are accepted, different sponsorship options, and how the generated funds are utilized.

### How to update information

- YAML files must be stored in the `config/finance` directory.

- Create separate folders for each year under `config/finance`, such as `config/finance/2023`. Inside each year's folder, include two YAML files: `Expenses.yml` and `ExpensesLink.yml`.

- In `Expenses.yml`, record expenses for each month, specifying the `Category` and `Amount`.

- In `ExpensesLink.yml`, provide discussion links related to expense categories.

- When a new year begins, create a corresponding folder for that year under `config/finance` and place the YAML files inside the folder for that specific year. For example, create a folder named `config/finance/2024` for the year 2024 and `config/finance/2025` for the year 2025. Place the YAML file for each respective year inside its designated folder.

- Modify the years within the `scripts/finance/index.js` , `lib/getUniqueCategories.js` and `components/FinancialSummary/BarChartComponent.js` to handle data for different years effectively.

## Case studies

### Overview

A case study is a special document that any end-user company can provide. An end-user company is a company that uses AsyncAPI to solve technical challenges. A case study is not a document where a vendor company can describe how they build their commercial AsyncAPI-based product. On the other hand, it is completely fine if a case study of some end-user mentions some commercial tools that helped them to work with AsyncAPI or event-driven architecture. An example of such a case can be a case study from an end-user where at some point, Confluent Schema Registry is mentioned in an explanation about schemas and runtime message validation.

### How to add a case study

A case study is documented in the form of a YAML file. Anyone can open a pull request with a new case study.

- YAML file must be located in `config/casestudies`.
- To make it easier for you to create such a YAML file you can use:
  - Template YAML with comments explaining every section
  - JSON Schema that describes all YAML fields
- All additional files for the case study, like complete AsyncAPI document examples, should be located in the `public/resources/casestudies` directory.
- Company logo and other images that will be rendered in the website should be located in `public/img/casestudies`.

Once you collect all information and create a case study, open a pull request. It must be authored or at least approved by a representative of the given company. Such a representative is probably already a contact person mentioned in the case study.

A case study becomes publicly available right after merging and rebuilding the website.

## JSON Schema definitions

All AsyncAPI JSON Schema definition files are being served within the `/definitions/<file>` path. The content is being served from GH, in particular from https://github.com/asyncapi/spec-json-schemas/tree/master/schemas.
This is possible thanks to the following:

1. A [Netlify Rewrite rule](https://docs.netlify.com/routing/redirects/rewrites-proxies/) located in the [netlify.toml](netlify.toml) file, which acts as proxy for all requests to the `/definitions/<file>` path, serving the content from GH without having an HTTP redirect.
2. A [Netlify Edge Function](https://docs.netlify.com/netlify-labs/experimental-features/edge-functions/) that modifies the `Content-Type` header of the rewrite response to become `application/schema+json`. This lets tooling, such as [Hyperjump](https://json-schema.hyperjump.io), to fetch the schemas directly from their URL.  
  Please find a flowchart explaining the flow this edge function should accomplish:
  ```mermaid
  flowchart TD
    Request(Request) -->schema-related{Is it requesting Schemas?}
    schema-related -->|No| req_no_schemas[Let the original request go through]
    req_no_schemas --> Response(Response)
    schema-related -->|Yes| req_schemas[Fetch from GitHub]
    req_schemas-->req_json{Was requesting a .json file?}
    
    req_json -->|No| Response(Response)
    req_json -->|Yes| response_status{Response Status?}
    
    response_status -->|2xx| response_ok[OK]
    response_status -->|304| response_cached[Not Modified]
    response_status -->|Any other| response_ko

    response_ok --> set_headers[Set Response Content-Type header to application/schema+json]
    set_headers --> send_metric_success[Send success metric]
    response_cached -- cached:true --> send_metric_success
    response_ko --> send_metric_error[Send error metric]

    send_metric_success -- file served from raw GH --> Response(Response)
    send_metric_error --the errored response --> Response(Response)
  ```
   

## Project structure

This repository has the following structure:

<!-- If you make any changes in the project structure, remember to update it. -->

```text
  ├── .github                     # Definitions of GitHub workflows, pull request and issue templates
  ├── components                  # Various generic components such as "Button", "Figure", etc.
  ├── config                      # Transformed static data to display on the pages such as blog posts etc.
  ├── context                     # Various React's contexts used in website
  ├── css                         # Various CSS files
  ├── lib                         # Various JS code for preparing static data to render in pages
  ├── pages                       # Website's pages source. It includes raw markdown files and React page templates.
  │    ├── about                  # Raw blog for /about page
  │    ├── blog                   # Blog posts
  │    ├── docs                   # Blog for /docs/* pages
  │    └── tools                  # Various pages to describe tools
  ├── public                      # Data for site metadata and static blog such as images
  ├── scripts                     # Scripts used in the build and dev processes
  ├── next.config.js              # Next.js configuration file
  ├── netlify                     # Code that runs on Netlify
  │    ├── edge-functions         # Netlify Edge-Functions code
  ├── postcss.config.js           # PostCSS configuration file
  └── tailwind.config.js          # TailwindCSS configuration file
```