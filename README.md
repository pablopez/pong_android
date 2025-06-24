# Pong Web App

This project uses **Vue 3**, **TypeScript**, and **Tailwind CSS** to implement a basic Pong game. It is configured with **Vite** for bundling and **Capacitor** to build a native Android APK.

The paddle can be controlled with the mouse, by dragging on touch screens, or
using the left and right arrow keys when a keyboard is available.

## Development

Install dependencies:

```bash
npm install
```

Run the development server:

```bash
npm run dev
```

## Build for Web

```bash
npm run build
```

The output will be placed in the `dist` folder.

## Build for Android

1. Install Capacitor's Android platform:

```bash
npx cap add android
```

2. Copy the latest build into the native project:

```bash
npm run build
npx cap copy android
```

3. Open Android Studio to build and run the APK:

```bash
npx cap open android
```

## Testing

Currently there are no automated tests. The `npm test` script simply prints a message.
