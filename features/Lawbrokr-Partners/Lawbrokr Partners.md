
Lawbrokr Partners
# Initial Scope

Hi Claude. I have a Partner Program for Lawbrokr where we track client referrals and payouts so that our partners have access to a back end where they know how where their deals are in the pipeline, and how they are doing overall.

I'd like to create this in house. Right now I'm using First Promoter to do this.

This is the sign up sheet: [https://lawbrokr.firstpromoter.com/?host=lawbrokr.firstpromoter.com](https://lawbrokr.firstpromoter.com/?host=lawbrokr.firstpromoter.com)

The image I attached is my backend where I can manage everything, Second image is what the partner sees.

Can you help with this?

Mar 24

Reading frontend design skill

Reading frontend design skill

Before diving in, I want to make sure I build exactly what you need. This is a meaningful project — essentially replacing FirstPromoter with a custom-built partner portal. Let me understand the scope better.

Q: What would you like to start with? A: Both — show me a full prototype of both sides

Q: What data/integrations does this need to connect to? (Select all that apply) A: Manual data entry for now

Q: What's most important to get right in v1? (Select all that apply) A: Deal pipeline visibility for partners, Commission tracking & payouts, Referral link generation, Partner onboarding flow

Mar 24

Perfect — I'll build you a full working prototype with both the admin side and the partner-facing portal, all in one app. This will include partner management, deal pipeline, commissions, referral links, and the partner onboarding flow.

visualize

visualize show_widget

Here's your full Lawbrokr partner program prototype. Toggle between **Admin view** and **Partner view** using the switcher at the bottom-left.

**Admin side includes:**

- Dashboard with revenue, partner count, referrals, and pending commissions
- Partners table with referral codes and commission tracking
- Kanban pipeline (New → In Progress → Closed Won → Lost)
- Commission ledger with pay buttons
- Referrals log with all activity

**Partner side includes:**

- Personalized home with earnings summary and payout alert
- Onboarding checklist (account → terms → payout method → first referral)
- Referral link with click stats
- My deals view showing pipeline visibility
- Commission and payout history

**What's next:** This is a functional React/HTML prototype. To turn it into a real product, the main decisions are:

1. **Where does data live?** — a simple database like Supabase or Airtable works well for this scale
2. **Authentication** — do partners log in via email link (magic link) or password?
3. **Payouts** — manual bank transfer, Stripe, or a payout platform like Trolley?

Want me to build out any specific section in more detail, or start wiring it up to a real backend?

---


