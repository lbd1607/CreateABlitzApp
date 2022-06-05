<!-- no toc -->
# Create a Blitz.js App

  - [Initial Setup](#initial-setup)
  - [Configure Postgres](#configure-postgres)
    - [Initial Postgres Installation and Configuration (Local DB)](#initial-postgres-installation-and-configuration-local-db)
    - [Configure Blitz to Use Your Local Postgres Database](#configure-blitz-to-use-your-local-postgres-database)
    - [Create another database and user for testing](#create-another-database-and-user-for-testing)
  - [Create Database Schemas and Generate Models](#create-database-schemas-and-generate-models)
  - [Set Up Your Project Files](#set-up-your-project-files)
  - [Optional Stuff](#optional-stuff)
    - [Add Tailwind to Your Project](#add-tailwind-to-your-project)
    - [Add Other Non-Recipe Packages to Your Project](#add-other-non-recipe-packages-to-your-project)
    - [Use FontAwesome Icons in Your Project](#use-fontawesome-icons-in-your-project)
    - [Backups](#backups)
  - [Troubleshooting](#troubleshooting)
    - [I Can’t install Blitz](#i-cant-install-blitz)
    - [The App Won’t Compile](#the-app-wont-compile)
    - [React-Final-Form Causes an Internal Server Error](#react-final-form-causes-an-internal-server-error)

This tutorial walks you through how to set up a Blitz app from the beginning. For more information, refer to [The Blitz Docs](https://blitzjs.com/docs/get-started).

## Initial Setup

  1. Install Blitz: `npm i -g blitz`. If you have trouble installing Blitz, refer to [Troubleshooting: I Can’t install Blitz](#i-cant-install-blitz).
  2. To ensure Blitz has been installed, check the version: `blitz -v`.
  3. Create new project: cd into your project directory. Run `blitz new <yourProjectName>`.
  4. Open the project in vscode.
  5. Start project: `cd` into the project that you just created and run `blitz dev`.
  6. If you want to run the server for production build the app then start the server: `blitz build` then `blitz start`.

For the basics and a list of commands, check out the `README.md` in the root of the app.

## Configure Postgres

> **Note:** If you just want to use the included sqlite database, skip these steps.

### Initial Postgres Installation and Configuration (Local DB)

  1. Open a new terminal window and go to your home directory: `cd ~/`.
  2. Get updates: `sudo apt update`.
  3. Install postgres: `sudo apt install postgresql`.
  4. Login as root user: `sudo --login --user=postgres`.
  5. Configure initial database settings: `sudo -u postgres psql`.
  6. Set a password: `\password postgres`, press Enter. Type the password and confirm it.

### Configure Blitz to Use Your Local Postgres Database

  1. In `schema.prisma`, go to `datasource` db and change the `provider` from `sqlite` to `postgres`.
  2. In `env.local`, comment out `sqlite` and uncomment `postgres`. Set postgres to follow the format: `postgres://<dbuser>:<dbpassword>@localhost:5432/<database>`.
  3. In vscode, delete the entire migrations folder.
  4. In your project folder, run `blitz prisma migrate dev --preview-feature`. If you’re asked to reset the database, select `Yes`.
  5. Start the server: `blitz dev`.

### Create another database and user for testing

To use Jest for testing, you’ll need to set up another database.

  1. Open a new terminal window and go to home directory: `cd ~/`.
  2. Login as root user: `sudo --login –user=postgres`.
  3. Create a testing database: `createdb testdb`.
  4. Create a user: `createuser tester`.
  5. Go into psql: `psql`.
  6. Set a password: `\password tester`, press Enter. Type the password and confirm it.
  7. Grant user connection to db: `grant connect on database testdb to tester;`.
  8. Grant user db permissions: `grant all privileges on database testdb to tester;`.
  9. Type `\q` to quit.
  10. Type `logout` to log out.
  11. Configure `.env.test.local` to use your new user: `postgres://<dbuser>:<dbpassword>@localhost:5432/<database>`.
  12. To make sure this worked, go to your project folder and run `yarn test`.

## Create Database Schemas and Generate Models

> **DANGER ZONE:** To make changes to existing schemas, edit `schema.prisma` then **only migrate** the database. Regenerating the models after these initial steps will overwrite all existing code!

  1. In `schema.prisma`, create all of your models.
  2. For each model, generate files: `blitz generate all <modelname>`.
  3. To generate a model that is a child of another model, run the command `blitz generate all <child> --parent <parentproject>`.
  4. Migrate the database: `blitz prisma migrate dev --preview-feature`.

## Set Up Your Project Files

  1. Update zod validation schema names in the Create, Update, and Delete mutations as needed. The Delete mutations only need modifications for manual cascade deletions. If pulling in data from another page, add it to zod object in your Create mutations.
  2. Update the model’s `form .tsx` file. If necessary, import final form and fields.
  3. Update `[projectid].tsx` and `edit.tsx` to display `name` instead of `id`.

## Optional Stuff

### Add Tailwind to Your Project

- Add Tailwind to your project with a Blitz recipe: `blitz install tailwind`.
- Config custom settings and class definitions in `tailwind.config.css`
- Create base styles (for example, `h1`) in **core > styles > index.css**.
- You must add an import in `_app.tsx` to use Tailwind in your app: `import "app/core/styles/index.css"`

### Add Other Non-Recipe Packages to Your Project

For most packages, back up your app first, then use yarn to install your packages.

To add other React-Final-Form packages specifically, try to update first so your node packages don’t get messed up. For example, run `npm update --save react-final-form-listeners` rather than `npm install …`. Otherwise, merge a copy of your `node_modules` folder after it is modified and be sure to check for dependencies in `package.lock files`. For others, backup the `node_modules` folder just in case, `cd` to the main project folder, and run the appropriate yarn or npm install command.

### Use FontAwesome Icons in Your Project

To add FontAwesome to your project, install the following:

- `yarn add @fortawesome/free-brands-svg-icons`
- `yarn add @fortawesome/fontawesome-svg-core`
- `yarn add @fortawesome/free-solid-svg-icons`
- `yarn add @fortawesome/react-fontawesome`

First, Add all the icons that you want to use in `_app.tsx`:

- `import { faHeartbeat } from "@fortawesome/free-solid-svg-icons"`
- Add the icon you imported in the `library.add` statement in `app.tsx`

Then to access them locally, add these imports to your the file:

- `import { FontAwesomeIcon } from "@fortawesome/react-fontawesome"`
- `import "@fortawesome/fontawesome-svg-core/styles.css"`

### Backups

Because this is still a beta framework, it’s best to keep at least one full backup around if you need to restore something like node modules. Git is great for backing up code, but it doesn’t receive local files that are vital in getting the app to compile and run correctly.

## Troubleshooting

### I Can’t install Blitz

This is likely due to a conflict between node packages or package managers.

If you can’t resolve the issue, you may need to uninstall and purge nvm, node/nodejs, and npm. Then, reinstall nvm, install node, and install yarn:

  1. Deactivate nvm: `nvm deactivate`.
  2. Uninstall nvm: `nvm uninstall <version>`.
  3. Remove nvm folder: `rm -Rf ~/.nvm`.
  4. Uninstall node: `sudo apt-get purge nodejs`.
  5. Uninstall npm: `sudo apt-get purge npm`.
  6. Get updates: `sudo apt-get update`.
  7. Install `nvm` with `curl`.
  8. Set source file: `source .bashrc`.
  9. Check nvm version: `nvm -v`.
  10. Install node: `nvm install node`.
  11. Set use to newest version of node: `nvm use node`.
  12. Install yarn: `npm install -g yarn`.
  13. Get updates: `sudo apt-get update`.
  14. Install Blitz: `npm i -g blitz`.

### The App Won’t Compile

This is likely due to a conflict in node packages. If available, copy the full `node_modules` folder from a backup and replace the existing one. Remember the only folder that truly matters (because it contains all of your code) is the app folder inside the Blitz app.

### React-Final-Form Causes an Internal Server Error

It could be because you’re using the default `<Form />` component from `react-final-form`. Blitz has its own `<Form/>` component. If you want to use the component from React-Final-From, import and use it with an alias, such as `FinalForm`.
