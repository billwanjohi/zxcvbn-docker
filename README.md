# zxcvbn-docker

A simple docker setup for securely testing strength of your passwords.

## Rationale

[zxcvbn](https://github.com/dropbox/zxcvbn) is a fantastic password strength estimator maintained by Dropbox.

There are a number of ways you can use it to evaluate your own passwords,
for example via the Bitwarden
[Password Strength Testing Tool](https://bitwarden.com/password-strength/)
webpage.

This local solution may be a better solution for you, for a couple of reasons:

### Zero Trust

I regard Bitwarden (and Dropbox) as trustworthy organizations, but by testing in an ephemeral and non-network-connected docker container, I don't need to worry about the implementation of the library or the webpage it's hosted on. Even supply chain attacks on the node package manager can't threaten leakage of my tested credentials, because there is nowhere for the exfiltrated data to go.

You do still need to trust:

- my `docker-compose.yaml` file, to appropriately cut off networking and not allow any writes to your host
- the container runtime, to enforce the rules I specified in this repository
- your terminal emulator, to appropriately flush your buffer when you close it
- your operating system, to not be recording screenshots
- your keyboard (and USB hub), to not have a keylogger

### More Detail

While the Bitwarden site and most tools offer an all-in assessment of password strength,
zxcvbn actually presents quite a bit more detail that I haven't seen exposed in a web interface.

If you want some more detail on, for example, how a cracker might split your password into more easily-guessed chunks, this raw output might be for you.

<details>
<summary>Example Output</summary>

```javascript
{
  password: 'correcthorse',
  guesses: 1608280,
  guesses_log10: 6.206361661246315,
  sequence: [
    {
      pattern: 'dictionary',
      i: 0,
      j: 6,
      token: 'correct',
      matched_word: 'correct',
      rank: 1140,
      dictionary_name: 'us_tv_and_film',
      reversed: false,
      l33t: false,
      base_guesses: 1140,
      uppercase_variations: 1,
      l33t_variations: 1,
      guesses: 1140,
      guesses_log10: 3.0569048513364723
    },
    {
      pattern: 'dictionary',
      i: 7,
      j: 11,
      token: 'horse',
      matched_word: 'horse',
      rank: 701,
      dictionary_name: 'passwords',
      reversed: false,
      l33t: false,
      base_guesses: 701,
      uppercase_variations: 1,
      l33t_variations: 1,
      guesses: 701,
      guesses_log10: 2.8457180179666586
    }
  ],
  calc_time: 22,
  crack_times_seconds: {
    online_throttling_100_per_hour: 57898080,
    online_no_throttling_10_per_second: 160828,
    offline_slow_hashing_1e4_per_second: 160.828,
    offline_fast_hashing_1e10_per_second: 0.000160828
  },
  crack_times_display: {
    online_throttling_100_per_hour: '2 years',
    online_no_throttling_10_per_second: '2 days',
    offline_slow_hashing_1e4_per_second: '3 minutes',
    offline_fast_hashing_1e10_per_second: 'less than a second'
  },
  score: 2,
  feedback: {
    warning: '',
    suggestions: [ 'Add another word or two. Uncommon words are better.' ]
  }
}
```
</details>

## Requirements

- [Docker Compose](https://docs.docker.com/compose/install/)

## Execution

```shell
❯ docker compose run --rm cli
```

The --rm option tells Docker to dispose of the container on exit

```javascript
> score('correcthorse')
```

I recommend you transfer any results by manually typing into a password manager or writing with pen and paper,
so that you don't have to worry about clipboard access.

## Cleanup

1.  Call `process.exit()` or press ctrl-d to exit the container
2.  [optional] Run the following to be sure that you have no dangling containers or volumes left over:

    ```shell
    ❯ docker compose down --remove-orphans --volumes
    Warning: No resource found to remove for project "zxcvbn-docker".
    ```

    (There should be no resources, but it doesn't hurt to verify.)

3.  Close the terminal tab/window as soon as you are done testing

And that's it! There should be no trace of your tests at this point.
