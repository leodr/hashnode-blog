## The Process of Building Quice, the Headless Form Submissions Service

Unfortunately, I heard about the Hashnode x Vercel Hackathon a little bit late. I saw the Tweet announcement on January 28, which got me fired up. I messaged two of my colleagues if they wanted to participate with me, only to cancel on them 5 minutes later when I found out that this event is for individuals only.

Even though I enjoy group projects more, I did not let this take away my motivation! But feel free to take this as a suggestion and host a group-friendly Hackathon in the future, that would be even more awesome!

I really like Vercel and have some projects hosted on there, so I already knew how fun it is to spin up a new project there. To be honest, until that point I have not really heard all that much about Hashnode. I definitely read some blogs that are powered by Hashnode, but never realized that (it is a good thing to put the content in front, not the company). 

So I tried it and boom! It is great, and I honestly really appreciate the markdown editor. Markdown is a familiar format to developers that is quick to write and always feels a little bit safer and more "exportable" (if that is even a word) than something like Google Docs or Microsoft Word.

Anyways, enough chatter let's get to the project!

## My Project: Quice, a Headless Form Submission Service

For the hackathon I built  [Quice](https://github.com/leodr/quice), which is an open-source, self-hostable, headless form submission service. 

It works very simply: You send arbitrary form data as JSON to a REST endpoint, which stores the data in Cloud Firestore and presents it in a neat little interface:

![screenshot.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1612730562551/0VK5E4y-1.png)

I have a publicly available demo of the app [here](https://quice.io), take a look and click around! You can use the test account `test@quice.io` with the password `password` if you don't want to create an account.

The whole thing is built on Next.js combined with NestJS for the backend parts. NestJS is integrated as a Next.js API-Route, which is something I figured out a couple of days ago (blog post topic alarm!).

## Inspiration

I have been working on [my personal site](https://github.com/leodr/leodriesch.com/tree/develop) for some time and part of it is a regular contact page, just like basically every website has it. You put in your name, and email address, and a message and submit that, so I can get back to you.

The UI was built pretty quick, but then I realized that this data also has to reach me in one way or another. I checked out some of the existing solutions for form submissions, but they all seemed a little bit too much for my simple use-case.

I ended up implementing the form submission on my site with a server-side function that sends me an email with the submitted form data via SendGrid. This is everything but optimal, but it works for my simple use-case and I get to use the free limits of SendGrid.

However, simple form submission made it on my "things somebody should build better" list, which most of the time just gets added to and things are rarely checked off 😢. And when I heard about the hackathon I thought: Yup, this is my opportunity!

Initially, I was playing with the idea of creating some sort of SaaS application or it. I quickly realized that the scope of this was way too big for the 10 days I had left at that point. It would also not work that well as a hackathon project, since it would have to be (at least parts of it) closed source.

I took a step back and thought about how I could embrace one of the themes of the hackathon. The thing that came to my mind was how easy it is with Vercel to deploy a new project from a template. I have seen quite a few READMEs of projects (e.g. [Nextra](https://github.com/shuding/nextra)) that used the [Vercel deploy button](https://vercel.com/docs/more/deploy-button).

This neat little button invokes a full project creation flow, which clones the template to your GitHub account, sets up a project on Vercel, asks for the required environment variables, and sets up the handy Push-To-Deploy functionality that Vercel is known for.

So I put 2 and 2 together and formed the idea in my head: An open-source form submission service that everybody could deploy on their own Vercel account in a couple of minutes!

## My Journey of Building It

I saw the Tweet about the Hackathon while mindlessly scrolling through Twitter during my uni lecture and instantly started thinking about how to do this project. I came up with the following list of technology I wanted to use, based on the short time constraints and the deployment strategy:

#### 1. Next.js

I have used it in the past, know it, and love its fast-paced development workflow. It also works perfectly with Vercel and provides serverless functions for that little bit of backend logic I would need. No brainer.

#### 2. TailwindUI

I knew that I would have fewer days than a lot of the other participants and if I wanted to compete with them, I would not only have to work hard but also move as fast as I could.

TailwindUI helped me to do just that: I could copy entire pre-made and pre-designed pages of HTML, convert them to JSX using a [neat little VSCode extension](https://github.com/leodr/paste-html-as-jsx) I made.

I could rapidly create new UI elements and pages, while easily maintaining a high standard for accessibility.

#### 3. TailwindCSS

Well, using TailwindUI already implied this, but it is still worthy to mention. One of the main benefits I see with Tailwind is that you do not have to switch between files to add styling. With [Emmet](https://emmet.io/) shorthands, it is SUPER quick to create fully styled elements, without ever having to leave your JavaScript code or think about how you name a CSS class.

But I am not going to lie, sometimes the JSX does look a little bit nasty when compared to using something like [`styled-components`](https://styled-components.com/) or traditional CSS files.

#### 4. NestJS

I have never really been a backend type of guy. I know some ASP.NET Core and the basics of ExpressJS, but I never got serious with it.

NestJS provides a clear structure for applications with file generators that help you enforce it. So for me, NestJS is all I want from a backend framework. The documentation is awesome and it naturally keeps me from creating architectural mistakes.

Since I really enjoy the simplicity of [Next.js API routes](https://nextjs.org/docs/api-routes/introduction), I chose to integrate the NestJS backend into one "monolithic" API route. This allowed me to keep everything in one project, which applied most of Next.js' benefits also to my backend:

- TypeScript support
- Hot reloading
- Easy deployment with Vercel

Big productivity win!

### The Datastore

So what is missing? A datastore.

Unfortunately, Vercel does not provide any database service themselves (yet?), so I had to choose an external provider.

A big constraint was the fact that the project would be self-hosted by users. So the setup has to be simple, which basically filters out every product that AWS has (I think the AWS console is just next level chaotic).

It should also have a generous free tier, so people can use it alongside Vercel's hobby tier to host small-scale Quice instances for free. This was the main factor that sorted out [Heroku's free tier of PostgreSQL](https://elements.heroku.com/addons/heroku-postgresql) since it is limited to only 10,000 rows and 1GB of storage.

So the database provider of my choice became Cloud Firestore by Firebase. It is easy to set up, has a generous free tier, is directly accessible from the frontend. Its real-time queries also remove the need for a client-site state management solution.

I could also use Firebase Authentication with it, which made everything even more simple!

### The Building Part

Ok, enough talking about technologies, let's start with the building part! The minute my uni lecture ended that day, I spun up the Next.js project and spent about an hour setting up tools like ESLint, Prettier, TypeScript, and Git hooks (we all know how it is in the JavaScript ecosystem 😄). But I would not want to miss out on the confidence these tools give me in the code I write for the cheap, mostly one-time cost of setting them up.

Next, I started building a visual identity for the project. For the brand color, I settled on the "rose", similar to the Airbnb. It is rare to see on websites and differentiates the brand I created from TailwindUI, which mainly uses Indigo.

I also came up with a logo:

![Hashnode Publiciation Logo.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1612735051528/xwdmCYfbk.png)

It was basically supposed to be a piece of paper (like a survey) lying on a table with a pen on top of it. I know it is very abstract, but hey, that's how most logos are these days!

>  [Here's](https://www.figma.com/file/d1TlHxcyflRUU1X3BU3xtd/Quice-Artwork?node-id=20%3A4)  the Figma file with all of the designs I made for Quice, if you're interested.

Then I built the entire authentication flow in a couple of hours thanks to the easy-to-use Firebase authentication SDK and  [this](https://tailwindui.com/components/application-ui/forms/sign-in-forms#component-bc08eb211afa45fad7c9f89c1891f284) great TailwindUI example.

One big productivity boost I've had was coding a script that would write large amounts of realistic example data into my database. I find it difficult to create a user interface without having data to display or having to inject some data manually each time you need it.

### The Backend

Thanks to the Firebase JavaScript library you can write most of your code on the client-side. But for this project, some of the code would have to run on a server, mainly the API endpoint to which you would send submissions, as well as the process of deleting a form with all of its submissions since it is very time-consuming.

As noted earlier, I chose to implement the Backend logic with NestJS, "a progressive Node.js framework for building efficient, reliable and scalable server-side applications." ([source](https://nestjs.com/)).

I embedded the NestJS application into a [catch-all API route](https://nextjs.org/docs/api-routes/dynamic-api-routes#catch-all-api-routes) in Next.js, which basically takes all traffic that arrives at a path of `/api/*` and gives it to NestJS to deal with it:

```ts
import { NestFactory } from "@nestjs/core";
import { ExpressAdapter } from "@nestjs/platform-express";
import { AppModule } from "api/app.module";
import express from "express";
import type { NextApiRequest, NextApiResponse } from "next";

const server = express();

const appPromise = NestFactory.create(
  AppModule,
  new ExpressAdapter(server)
).then((app) => {
  app.enableCors();
  app.setGlobalPrefix("/api");
  return app.init();
});

export default async function nestHandler(
  req: NextApiRequest,
  res: NextApiResponse
) {
  await appPromise;

  server(req, res);
}

export const config = {
  api: {
    bodyParser: false,
    externalResolver: true,
  },
};
```

This worked out pretty well, after figuring out that I would have to disable the integrated body-parser of Next.js since it confuses NestJS.

### Building the Application with TailwindUI

This has been an interesting experience. I have worked with TailwindUI a little bit beforehand, but never a project of this scale. 

For anyone that has not heard of it, TailwindUI is basically a library of HTML snippets that are styled with TailwindCSS.

For my workflow that basically meant that I would copy large amounts of HTML code into my React codebase and then clean it up. So instead of building up to code from scratch, it is the other way around. You copy in a bunch of code, strip everything you don't need, extract React components and adjust where needed. 

This workflow turned out to work pretty well! You see quick gains that keep your motivation levels sky-high and the nerd in me really liked the code refactoring sessions 😆.

One of the most complex components to build was the infinite, virtualized list for form submissions (in red):

![submission-list.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1612736918465/bWce4WHej.png)

I wanted to have the user experience of being able to scroll down the list as far as you want, but it should stay performant at the same time. That's right where my new favorite library for virtualized lists in React fits in:  [React Virtuoso](https://virtuoso.dev/).

It is easy to integrate and dynamic by default, which means you do not have to specify how tall each list item is, the library will compute it on the fly.

If you are interested in the implementation of this list, check it out  [here](https://github.com/leodr/quice/blob/develop/src/components/SubmissionList.tsx).

To power the list I had to build a custom hook, that subscribes to a Firebase query basically "infinitely", so you can always load more entries of that query, while it still receives real-time updates from Firebase. I had to build that one myself since I did not really find anything about it on the internet (blog post topic alarm!).

If you are interested in this implementation, check it out [here](https://github.com/leodr/quice/blob/develop/src/hooks/useSubmissionQuery.ts)!

### Writing the README

A cool README file goes a long way for an open-source project. It will often be the first contact point with any potential user, so it has to look crisp and clean!

For my project, it also served as the main source of documentation, as I did not have any spare time to code up a documentation website.

I am pretty happy with the [README design I made for Quice](https://github.com/leodr/quice#readme). It is clean, informative, and works well in both light- and dark-mode (check if your READMEs do after GitHub introduced dark mode!).

![Bildschirmfoto 2021-02-08 um 00.20.47.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1612740056366/K5QkN1GMr.png)

Here are some tips I usually use to improve my READMEs, although it would be cool to write a full blog post about it (blog post topic alarm!):

- Switch it up! Using only text is boring. Use images, icons, and emojis!
- Use the `align="center"` attribute (but not for bodies of text)!
- Give it some white-space! Use the `<br />`-tag to create blank lines. Let your paragraphs breathe!

## Conclusion

The hackathon was hella fun! The motivation those quick projects with tight deadlines give me is amazing. I was working on the project almost every day till late in the evening and in my opinion it turned out great! 

I got almost everything done that I intended to do at the beginning. However I would say that my time management could definitely be improved, otherwise, I would not have to write the last bits of this article at 2 AM here in Germany.

So I learned again that you should tie down the scope of a project as much as you can initially and then expand instead of the other way around.

I am excited to hear what you think about my project! And thanks to Hashnode and Vercel for hosting this event, it spices up the rather boring times we have at the moment!

Cheers!





















