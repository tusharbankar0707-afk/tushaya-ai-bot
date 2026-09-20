# tushaya-ai-bot

AI-powered assistant for bug bounty hunters and pentesters, built with Next.js and Supabase. Chat with an AI and run open-source tools like Subfinder, Katana, Nuclei and httpx through a plugin system.

> **Credit:** This project is based on [HackerGPT-2.0 (PentestGPT)](https://github.com/Hacker-GPT/HackerGPT-2.0) by the Hacker-GPT / HackerAI team, and is distributed under the same license (GNU GPL v3.0). See the [license](license) file.

## What Makes This Special?

This assistant does not only answer hacking questions, it can also help you work with widely used open-source security tools. To see all available tools, open the Plugin Store. If you need a quick guide on a specific tool such as Subfinder, select the tool and type `/subfinder -h`.

Some of the notable tools:

- **[Subfinder](https://github.com/projectdiscovery/subfinder)** is a subdomain discovery tool designed to enumerate and uncover valid subdomains of websites efficiently through passive online sources.
- **[Katana](https://github.com/projectdiscovery/katana)** is a next-generation crawling and spidering framework designed for robust, efficient web enumeration.
- **[Port Scanner (Naabu)](https://github.com/projectdiscovery/naabu)** is a high-speed port scanning tool, focused on delivering efficient and reliable network exploration.

You can use these tools without typing complex commands. Simply select the tool you want and describe in your own words what you need to do.

More tools are available in the Plugin Store.

> **Use responsibly.** Only test systems that you own or have written permission to test.

## Important Note About Running Locally

You can run this project locally, but the RAG system, plugins, and some other features will only work with proper and more complex configuration (API keys and external services).

## Local Quickstart

Follow these steps to get your own instance running locally.

### Requirements

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/) v20 (see `.nvmrc`)
- [Docker Desktop](https://docs.docker.com/get-docker) (needed to run Supabase locally)

### 1. Clone the Repo

```bash
git clone https://github.com/tusharbankar0707-afk/tushaya-ai-bot.git
cd tushaya-ai-bot
```

### 2. Install Dependencies

In the root directory of the repository, run:

```bash
npm install
```

### 3. Install Supabase & Run Locally

#### Why Supabase?

Previously, local browser storage was used to store data. This was not a good solution for a few reasons:

- Security issues
- Limited storage
- Limits multi-modal use cases

Supabase is easy to use, open-source, based on Postgres, and has a free tier for hosted instances.

#### 1. Install Docker

Install and start [Docker Desktop](https://docs.docker.com/get-docker). Supabase needs Docker running.

#### 2. Install Supabase CLI

**macOS / Linux**

```bash
brew install supabase/tap/supabase
```

**Windows (option A: Scoop)**

```powershell
scoop bucket add supabase https://github.com/supabase/scoop-bucket.git
scoop install supabase
```

**Windows (option B: without Scoop)**

Install the CLI into the project using npm:

```powershell
npm install supabase --save-dev
```

After this, use `npx supabase ...` instead of `supabase ...` in the commands below. The `npm run` scripts will find it automatically.

#### 3. Start Supabase

In the root of the repository, run:

```bash
supabase start
```

On Windows with option B:

```powershell
npx supabase start
```

### 4. Fill in Secrets

#### 1. Environment Variables

In the root of the repository, create your environment file.

macOS / Linux:

```bash
cp .env.local.example .env.local
```

Windows (PowerShell / Command Prompt):

```powershell
copy .env.local.example .env.local
```

Get the required values by running:

```bash
supabase status
```

(On Windows option B: `npx supabase status`)

Note: Use `API URL` from `supabase status` for `NEXT_PUBLIC_SUPABASE_URL`.

Now open your `.env.local` file and fill in the values.

If an environment variable is set, it will disable the matching input in the user settings.

> **Never commit `.env.local` to GitHub.** It contains your secret keys. It is already listed in `.gitignore`.

#### 2. SQL Setup

In the 1st migration file `supabase/migrations/20240108234540_setup.sql` you need to replace 2 values with the values you got above:

- `project_url` (line 53): `http://supabase_kong_chatbotui:8000` (default) can remain unchanged if you don't change your `project_id` in the `config.toml` file
- `service_role_key` (line 54): the value from running `supabase status`

This prevents issues with storage files not being deleted properly.

### 5. Run the app locally

In the root of the repository, run:

```bash
npm run chat
```

Your local instance should now be running at [http://localhost:3000](http://localhost:3000). Use Node.js v20 (see `.nvmrc`).

You can view your backend GUI at [http://localhost:54323/project/default/editor](http://localhost:54323/project/default/editor).

### 6. Adding a local user

#### 1. Sign Up

Go to the login screen at [http://localhost:3000](http://localhost:3000). Fill in your email and password, then press Sign Up.

#### 2. Confirm email

Open Inbucket, the email testing service, at [http://localhost:54324](http://localhost:54324). Find the mailbox for the email you used to sign up and confirm your email.

Now you can log in with this email and password.

## Hosted Quickstart

Follow these steps to run your own instance in the cloud.

### 1. Follow the Local Quickstart

Repeat steps 1-4 in "Local Quickstart" above.

You will want separate repositories for your local and hosted instances. Create a new repository for your hosted instance on GitHub and push your code to it.

### 2. Setup Backend with Supabase

#### 1. Create a new project

Go to [Supabase](https://supabase.com/) and create a new project.

#### 2. Get Project Values

In the project dashboard, click the "Project Settings" icon at the far bottom left.

You will get the values for the following:

- `Project Ref`: found in "General settings" as "Reference ID"
- `Project ID`: found in the URL of your project dashboard (e.g. `https://supabase.com/dashboard/project/<YOUR_PROJECT_ID>/settings/general`)

While still in "Settings", click the "API" tab on the left. You will get:

- `Project URL`: found in "API Settings" as "Project URL"
- `Anon key`: found in "Project API keys" as "anon public"
- `Service role key`: found in "Project API keys" as "service_role" (treat this like a password!)

#### 3. Configure Auth

Click the "Authentication" icon on the far left. Under "Providers", make sure "Email" is enabled.

We recommend turning off "Confirm email" for your own personal instance.

#### 4. Connect to Hosted DB

Open your repository for the hosted instance.

In the 1st migration file `supabase/migrations/20240108234540_setup.sql` replace 2 values:

- `project_url` (line 53): use the `Project URL` value from above
- `service_role_key` (line 54): use the `Service role key` value from above

Now open a terminal in the root of the repository.

Login to Supabase:

```bash
supabase login
```

Link your project using the "Project ID" you got above:

```bash
supabase link --project-ref <project-id>
```

Push your database to Supabase:

```bash
supabase db push
```

Your hosted database should now be set up.

### 3. Setup Frontend with Vercel

Go to [Vercel](https://vercel.com/) and create a new project.

Import your GitHub repository for the hosted instance. In the project Settings, under "Build & Development Settings", set Framework Preset to "Next.js".

In environment variables, add:

- `NEXT_PUBLIC_SUPABASE_URL`
- `NEXT_PUBLIC_SUPABASE_ANON_KEY`
- `SUPABASE_SERVICE_ROLE_KEY`

You can also add API keys as environment variables:

- `OPENAI_API_KEY`
- `OPENROUTER_API_KEY`

For the full list of environment variables, see the `.env.local.example` file. If an API key is set as an environment variable, the matching input in the user settings is disabled.

Click "Deploy" and wait for the frontend to deploy. Once deployed, you can use your hosted instance at the URL Vercel gives you.

## Updating

In the root of the repository, run:

```bash
npm run update
```

If you run a hosted instance you also need to run:

```bash
npm run db-push
```

to apply the latest migrations to your live database.

## Questions or Issues

Open an [issue](https://github.com/tusharbankar0707-afk/tushaya-ai-bot/issues) on this repository.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for setup instructions and guidelines.

## Acknowledgements

This project is built on the work of the original HackerGPT / PentestGPT team and its contributors, including [@fkesheh](https://github.com/fkesheh), [@Fx64b](https://github.com/Fx64b) and [@josegranadosm](https://github.com/josegranadosm). Original repository: [Hacker-GPT/HackerGPT-2.0](https://github.com/Hacker-GPT/HackerGPT-2.0).

## License

Licensed under the [GNU General Public License v3.0](license).
