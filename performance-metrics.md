# Introduction

In this post, I wanna dive into Core Web Vitals and other performance metrics that are useful to measure the performance of a website.

# Core Web Vitals

## Largest Contentful Paint (LCP)

LCP measures the time it takes for the largest content element to be rendered on the screen.

It's a core user-centric metric that helps to measure the perceived loading speed of a page. It's one of Google's core web vitals and impacts the SEO of a website too.

A good LCP score is less than 2.5 seconds.

LCP tells you how quickly users perceive the main content of your page.

## Cumulative Layout Shift (CLS)

CLS measures the visual stability of a page. If a page renders, and suddenly the layout shifts, it's a bad user experience. Imagine you're on a site and the main content has loaded, but then suddenly a big ad appears, and the content you were reading moves down. That's a bad CLS score.

A good CLS score is less than 0.1.

A poor CLS (above 0.25) indicates elements are shifting around, damaging usability.

## Interaction to Next Paint (INP)

INP measures page responsiveness. It does so by tracking the latency of all interactions on a page, such as clicks, and keypresses.

We previously had FID for this, which INP replced.

A good INP is less than 200ms. It means the page responds quickly to user interactions.

# Other Performance Metrics

## First Contentful Paint (FCP)

FCP measures the time it takes for the first content element to be rendered on the screen.

It may not seem important, but FCP marks the first point the user sees something. This provides reassuarance that the page is loading.

It's not a Core Web Vital but still a good metric to track.

## Time to Interactive (TTI)

TTI measures the time it takes for a page to become fully interactive.

It's important, because a site is rarely useful if it's not interactive.

## Total Blocking Time (TBT)

TBT measures the total amount of time between First Contentful Paint (FCP) and Time to Interactive (TTI) where the main thread was blocked for too long and the user couldn't interact with the page.

TBT tells you how long a user had to wait before they could interact with the page.

A good TBT is less than 300ms.

## Speed Index

Speed Index measures how quickly content is visually displayed during page load.

It's important because it tells you how quickly the user sees the content. It goes hand in hand with metrics like LCP and FCP to give a fuller picture into the user's experience.
