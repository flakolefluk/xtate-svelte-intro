<script lang="ts">
  import { createMachine, assign } from 'xstate';
  import { useMachine } from '@xstate/svelte';

  const enum States {
    Enabled = 'Enabled',
    Disabled = 'Disabled',
  }

  const enum Events {
    Increment = 'Increment',
    Decrement = 'Decrement',
    ToggleEnabled = 'ToggleEnabled',
  }

  const increment = (ctx) => ctx.count + 1;
  const decrement = (ctx) => ctx.count - 1;

  // Set state machine
  const counterMachine = createMachine({
    initial: States.Enabled,
    context: {
      count: 0,
    },
    states: {
      [States.Enabled]: {
        on: {
          [Events.Increment]: {
            actions: assign({ count: increment }),
            cond: (ctx) => ctx.count < 10,
          },
          [Events.Decrement]: {
            actions: assign({ count: decrement }),
            cond: (ctx) => ctx.count > 0,
          },
          [Events.ToggleEnabled]: States.Disabled
        },
      },
      [States.Disabled]: {
        on: {
          [Events.ToggleEnabled]: States.Enabled
        },
      },
    },
  });

  const { state, send } = useMachine(counterMachine);
</script>

<p>The counter is {$state.value === States.Enabled ? "enabled": "disabled"}</p>
<p>Count: {$state.context.count}</p>
<button on:click={() => send(Events.Increment)}> Increment </button>
<button on:click={() => send(Events.Decrement)}> Decrement </button>
<button on:click={() => send(Events.ToggleEnabled)}> On/Off </button>

<style>
  button {
    font-family: inherit;
    font-size: inherit;
    padding: 1em 2em;
    color: #ff3e00;
    background-color: rgba(255, 62, 0, 0.1);
    border-radius: 2em;
    border: 2px solid rgba(255, 62, 0, 0);
    outline: none;
    width: 200px;
    font-variant-numeric: tabular-nums;
    cursor: pointer;
  }

  button:focus {
    border: 2px solid #ff3e00;
  }

  button:active {
    background-color: rgba(255, 62, 0, 0.2);
  }
</style>
