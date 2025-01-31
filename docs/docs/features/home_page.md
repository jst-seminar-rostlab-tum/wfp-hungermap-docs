import ReactPlayer from 'react-player';

# Home Page

Author: `Ahmed Farouk`

The HungerMap has undergone significant updates to improve user experience, performance, and accessibility. This document highlights the key differences between the old and new versions, focusing on both functional and non-functional improvements that make the map more efficient and user-friendly.

---

## Comparison Between Old and New HungerMap

### Old HungerMap

<ReactPlayer
url="/videos/home_page_old.mp4"
controls={true}
width="100%"
height="auto"
/>

### New HungerMap

<ReactPlayer
url="/videos/home_page_new.mp4"
controls={true}
width="100%"
height="auto"
/>

---

## New Features of HungerMap

### Non-Functional Features

| Feature                         | Old HungerMap                                                      | New HungerMap                                                                                         |
| ------------------------------- | ------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------- |
| **Performance Improvements**    | Slow performance, especially with complex map interactions.        | Enhanced performance with faster map rendering and smoother transitions between views.                |
| **General UI Improvements**     | Outdated, cluttered, and difficult to navigate.                    | Clean, modern UI with improved components for a more visually appealing and user-friendly experience. |
| **Error Handling**              | No error handling, the map often crashes or behaves unpredictably. | Improved error handling with clear messages and better management of issues.                          |
| **Loading Performance**         | Long loading time with no indication.                              | Improved with loading skeleton for map and components.                                                |
| **Accessibility Improvements**  | Not optimized for accessibility.                                   | Accessibility has been improved for better usability.                                                 |
| **Responsiveness Improvements** | Not optimized for various screens and sizes.                       | Fully responsive, now adapts seamlessly across all screen sizes and devices.                          |
| **Maintainability**             | Difficult to maintain and update due to complex code structure.    | Improved maintainability with cleaner, modularized code, making updates and bug fixes easier.         |

### Functional Features

| Feature                                  | Old HungerMap                                                | New HungerMap                                                                              |
| ---------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------------------------------------ |
| **Sidebar**                              | No sidebar, making navigation difficult.                     | Added a collapsible sidebar that hides and groups components to maintain focus on the map. |
| **Theme Switching**                      | Limited to dark theme.                                       | Allows switching between light and dark modes.                                             |
| **Countries with High Levels of Hunger** | No clear way to display this data.                           | New hunger alert table sorting countries with very high hunger levels.                     |
| **Chatbot**                              | Not available.                                               | Added a chatbot to help users navigate, ask questions about the map, and get insights.     |
| **Data Visibility**                      | Hard to distinguish between countries with and without data. | Improved with clear visual distinction.                                                    |
| **Country Selection in Country View**    | Must exit to global view to switch countries.                | Can switch between countries directly while in country view.                               |
| **Charts and Insights**                  | Charts lacked configuration options.                         | Charts can be customized (adjust x-axis, switch types, export data, enlarge view).         |
| **Legends and Info Section**             | Overloaded with information, hard to read.                   | More readable with continuous gradient and expandable info section.                        |
| **Hazard Alerts**                        | Scattered alerts with no grouping.                           | Similar alerts are grouped for better clarity.                                             |
| **Sharing Map View**                     | Not possible to share a specific map state.                  | Users can copy the link or share via the Share button.                                     |

---

## Conclusion

All the improvements made to the HungerMap, from enhanced performance and user-friendly design to advanced features like chatbots, have been implemented with one goal in mind: to provide a better, more intuitive user experience. The changes aim to offer a clearer, more accessible interface that ensures users can navigate, explore, and analyze hunger data with ease. By focusing on both functional and non-functional enhancements, we've ensured that the new map not only works faster but also looks and feels more modern, responsive, and reliable across all devices and screen sizes. These updates were designed to make the HungerMap not just a tool, but a more effective and engaging experience for users worldwide.
