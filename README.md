# My V0 Project - Tweet Time Machine

This project is designed to help you explore your Twitter history by uploading your tweets.js file from your Downloaded X/Twitter archive folder. With the app, you can view your past tweets and even hide retweets and replies for a more personalized experience.

I was looking for a way to read my tweets especially the thoughts I tweeted to remember. X only offers a vertical timeline which you have to endlessly scroll down to view posts so I wanted an alternative that takes away the labour of endless scrolling. My solution was between two options, a page of cards or a random tweet viewer. I opted for the latter for its element of surprise, a random tweet is fetched from your tweets.js file and displayed, a thought you probably forgot you posted.

The tech stack is mostly made up of libraries I kept seeing people on Instagram recommended. Basically the newest latest craze at the time, so this is basically a test project for trying them out.

## Tech Stack

This project is built with the following technologies:

- **[SvelteKit](https://kit.svelte.dev/)**: A framework for building robust, high-performance web applications.
- **[TypeScript](https://www.typescriptlang.org/)**: A typed superset of JavaScript that compiles to plain JavaScript.
- **[Tailwind CSS](https://tailwindcss.com/)**: A utility-first CSS framework for rapid UI development.
- **[Vite](https://vitejs.dev/)**: A fast build tool and development server.
- **[Aceternity UI](https://ui.aceternity.com/)**: A collection of UI components.
- **[Svelte Motion](https://motion.dev/svelte)**: An animation library for Svelte.

## Table of Contents

- [Getting Started](#getting-started)
- [Scripts](#scripts)
- [Project Structure](#project-structure)
- [Key Features](#key-features)
- [Dependencies](#dependencies)
- [Contributing](#contributing)
- [License](#license)

## Getting Started

To get started with the project, you'll need to have Node.js and pnpm installed on your machine. Follow these steps to set up the project:

1.  **Clone the repository:**

    ```bash
    git clone https://github.com/Passopla/redesigned-memory.git
    cd redesigned-memory
    ```

2.  **Install dependencies:**

    Run the following command to install all necessary dependencies:

    ```bash
    pnpm install
    ```

3.  **Run the development server:**

    Start the development server with:

    ```bash
    pnpm run dev
    ```

    This will start the server on [http://localhost:5173](http://localhost:5173).

## Scripts

The project includes several npm scripts for common tasks:

- `pnpm run dev`: Starts the development server.
- `pnpm run build`: Builds the application for production.
- `pnpm run preview`: Starts the production server to preview the build.
- `pnpm run check`: Runs the Svelte checker to find issues.

## Project Structure

The project follows a typical SvelteKit structure:

- **`src/`**: Contains the application's source code.
- **`src/lib/`**: Contains library code, such as components and utilities.
- **`src/routes/`**: Contains the application's pages. Each file in this directory corresponds to a route.
- **`static/`**: Static files like images and fonts.
- **`svelte.config.js`**: Configuration file for SvelteKit.

## Key Features

- **Upload Twitter Archive**: Upload your `tweets.js` file to view your past tweets.
- **Filter Tweets**: Filter tweets by time range (all time, recent, year, month, balanced).
- **Hide Retweets and Replies**: Option to hide retweets and replies for a cleaner view.
- **Random Tweet Display**: Display a random tweet from your archive based on selected filters.

## Dependencies

The project uses several key dependencies:

- **SvelteKit**: The Svelte framework for production.
- **Svelte**: A JavaScript library for building user interfaces.
- **Tailwind CSS**: A utility-first CSS framework for styling.
- **Aceternity UI**: A collection of UI components for Svelte.

For a complete list of dependencies, see the `package.json` file.

## Contributing

Contributions are welcome! Please fork the repository and submit a pull request for any improvements or bug fixes.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for more information.
