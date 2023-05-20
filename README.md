# Svelte + Muuri

I tried to use Muuri with Svelte, which kind of works, but it's not doing well.
For example, if I have 2 Kanban columns TODO and DONE, I can drag items in TODO to change its index position.
But if I want to drag an item from TODO to DONE, the DONE column seems not to trigger to receive an item.

I struggled with it for some days, but it's still not working.
Using vanilla JS without a framework like Svelte, Muuri seems to behave like expected.

So I hope you guys can help me with it 🙏🏼
I'm too impatient to read the docs of Muuri carefully and I'm a bit new to Svelte.

# Setup

`npm install bulma muuri`

`npm install --save-dev @iconify/svelte`

## Run the app

`npm run dev`
