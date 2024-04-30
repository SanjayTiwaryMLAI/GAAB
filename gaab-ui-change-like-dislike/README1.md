# Logo addition 
1. Go to the "public" folder and open the "index.html" file. Change the title to "<title>GeM Virtual Assistant</title>".

2. Replace the favicon in the "public" folder.
(./gaab-ui-change-like-dislike/source/ui-chat/public)

3. Create a new folder called "assets" in the "src" folder. Add the GeM SVG logo to this folder.

4. Paste the "index.d.ts" file in the "src" folder.

(gaab-ui-change-like-dislike/source/ui-chat/src/App.js)

5. Open the "app.js" file and add the following line to import the GeM logo: "import logo from './assets/gem-new-logo-v6.svg';"

6. Replace the code for the identity object at line number 77 in "app.js" with the following:

identity={{
    href: '/',
    title: useCaseName,
    logo: {
        src: logo,
        alt: 'GeM Logo'
    }
}}

# remove policy alert
7.Remove the alert code from app.js 

# Hyperlink
8.delete line 99 in chatMessage.tsx file.
(gaab-ui-change-like-dislike/source/ui-chat/src/components/ChatMessage.tsx)