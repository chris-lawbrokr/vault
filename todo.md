
Internal dash - account management tab
Justrice beta
Popups

Text messaging SMS


---

ROI Attribution
Chloe stats - pull integrations - ROI card in analytics page

Lawbrokr lead magnet - capture lead details from existing landing page forms **ai magic optimization**

---

The following are enhancements that can be made to improve popups and also ensure that it has analytics so that it can support tracking detailed analytics.

1. Add support for all of the CTA buttons in the popups (ignoring those for closing it) to append to the URL the engaged_popup parameter with the popup identifier. This allows for tracking views and conversions and attributing it to the popup.
    
    1. For reference look at the clips.js implementation there is an `appendEngagedClipParam` function, you want to add a similar function for all of the popup CTA buttons to include as part of the URL the engaged_popup parameter and set the popup id
    2. For clips the token is the clip that is specified, this would be the same for popups where there can be a `popup` defined OR alternatively if none is defined then the Rails API will make a best effort to determine the default configured popup and will regardless return the `popup` as part of the response. For testing purposes if you want to update your Mock API to return the popup you can include in the JSON payload `"popup": "popup_69b8ffaed7bb57f394fad11f98a52945"`
    
2. By default if no popup is provided to the Rails API then it will attempt to determine the default popup configured for the law firm. You need to also add support where if a user explicitly configures the popup then that is used and provided to the API url. You can look at Clips and mimic the implementation for the API request as it would be the same for Pop Ups except Pop Ups attempts to return the default popup for the firm if none is provided.
    1. The following is an example configuration where a popup is explicitly provided, like how clips is done:

<script>
    window.LawbrokrConfig = {
        popup: 'popup_69b8ffaed7bb57f394fad11f98a52945'
    };
</script>

1. Add support for robust defaults to ensure a default popup is displayed if a user forgets to configure styles/fonts/colors for it.
    1. First look at the code for Clips for making the API request and ensure that for Pop Ups it's similar and handles performing the API requests and retrying on error and dealing with delayed initialization and other common challenges when operating in different website platform environments.
    2. Add support for a set of defaults that can be used if a user has not configured them for the Pop Ups, these should make the defaults match the first pop up theme (black and white theme) so that even if a user does not configure it and many fields are null the default pop up will still look good.
    3. NOTE: There are many cases where a user will not have a popup configured, similarly to how Clips is implemented, if no Popup is configured then there will be a `204` error code returned see the Clips implementation in `fetchClipDetails` for reference, you will want to stop retrying and not render the Popup as there has not been any configured.