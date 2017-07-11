# project-iiif-mark

> IIIF based image marking project for PyBossa.

Presents a zoomable image, retrieved via a [IIIF Image API](http://iiif.io/api/image/2.1/), and asks volunteers to highlight specific
areas of that image (titles, names etc.).

Tasks are generated by choosing a task category from
[config/tasks.json](config/tasks.json) (e.g. `"title"`), a
IIIF manifest ID from
[config/metadata.csv](config/metadata.csv) and an optional JSON file
containing the results data of a previous *project-iiif-mark* project.

When a JSON results file is provided a task will be created for each result,
and each region now associated with that, the idea being that tasks can be
chained to highlight increasingly more specific regions in the text (e.g. all
names associated with a title). When a JSON results file is not used a task
will be created for each image.

## Configuring

By default, the project is configured for the LibCrowds playbills site. However,
alternative configurations can easily be created by making a copy of the
[config](config) folder, editing the files contained within and providing the
location of this custom config directory when running the scripts below.

## Build setup

Install [Node.js](https://nodejs.org/en/),
[Python](https://www.python.org/downloads/), and
[pbs](https://github.com/Scifabric/pbs), then:

```bash
# install
npm install

# clean
npm run clean

# generate project.json
npm run generate:context -- <task category> <manifest id> [--config=/path/to/config]

# generate tasks
npm run generate:tasks -- <task category> <manifest id> [--config=/path/to/config]

# build project
npm run build

# deploy
cd dist
pbs create_project
pbs add_tasks --tasks-file=tasks.json
pbs update_project
```

Once you have updated any additional settings on the server (category,
thumbnail, redundancy, webhooks etc.), the project is ready to be published.

## Developing

Build the project as above, then run the following:

```bash
# watch for JavaScript changes
npm run dev

# auto-update on the server
cd dist
pbs update_project --watch
```

### Defining new task sets

Task sets are defined in [config/tasks.json](config/tasks.json) using the
following structure:

```json
"category": {
  "name": "Appended to the catalogue title to create the project title",
  "description": "A one line description of the project",
  "objective": "The objective of the task",
  "guidance": "Additional guidance"
}
```

Note that if `guidance` is set to `null` and the task is being generated
from the results of a previous project then the guidance will be generated automatically in the form "Identify each *category* associated with
the highlighted *parent task category*.". So, category names should be
written in singular rather than plural form.
