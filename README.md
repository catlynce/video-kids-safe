# YouTube Video Manager

A modern Vue.js application for managing and watching YouTube videos with custom playback controls. Built with Vue 3, Vite, and TailwindCSS.

## Features

- Add YouTube videos to your personal collection via URL
- Custom video player with enhanced controls:
  - 10-second forward/backward seeking
  - Custom timestamp seeking
  - Progress tracking
  - Clean, minimal interface
- Persistent video list storage
- Thumbnail previews for all saved videos
- Responsive design with TailwindCSS

## Tech Stack

- Vue 3 with Composition API
- Vite for build tooling and development
- TailwindCSS for styling
- YouTube Player Plus for video playback
- Local Storage for data persistence

## Prerequisites

- Node.js (version specified in `.nvmrc`)
- Yarn package manager

## Installation

1. Clone the repository:
```bash
git clone [repository-url]
cd ytube-git
```

2. Install dependencies:
```bash
yarn install
```

## Development

Start the development server:
```bash
yarn dev
```

## Building for Production

Build the application:
```bash
yarn build
```

Preview the production build:
```bash
yarn preview
```

## Project Structure

```
ytube-git/
├── src/                # Source code
│   ├── App.vue        # Main application component
│   ├── components/    # Vue components
│   ├── assets/        # Static assets
│   ├── main.js        # Application entry point
│   └── style.css      # Global styles
├── public/            # Public static assets
└── ...configuration files
```

## Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
