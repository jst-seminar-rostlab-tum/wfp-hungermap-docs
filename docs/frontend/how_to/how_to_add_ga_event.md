# How to add new GA event

To add a new Google Analytics event, simply add the following code snippet where you want to send the event:

```typescript
window.gtag('event', 'event_name', params);
```

Replace `event_name` with the name of the new event. `params` can be any JavaScript object to send as optional parameters. It can also be omitted.

If you want to check whether the event is working, trigger the event and check the Realtime overview Report on the GA dashboard. You should see the event after a few seconds. However, to better visualize data, it is recommended to create a custom exploration for each event (type). To do this, go to Explore, then create a Blank exploration. Select the needed dimensions (usually Event name) and metrics (usually event count, etc.). To show only some type of events, add a filter to the event name at the bottom of the Settings section.