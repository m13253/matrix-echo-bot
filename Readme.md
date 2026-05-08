# matrix-echo-bot

This is an implementation of [MSC4295: Bot bounce limit](https://github.com/matrix-org/matrix-spec-proposals/pull/4295).

## Running the code

1. Create a Matrix account.

   I suggest registering your bot on [a self-hosted Synapse server](https://element-hq.github.io/synapse/latest/setup/installation.html), because running an echo-bot on a public server is very dangerous and often prohibited.

   ```
   $ register_new_matrix_user --no-admin -c homeserver.yaml -t bot
   ```

   To loosen the rate limit of Synapse, use the [`rc_*`](https://element-hq.github.io/synapse/latest/usage/configuration/config_documentation.html#ratelimiting) option.

   The bot account has to use password authentication. Multi-factor authentication and single sign-on are unsupported, as they can’t function unattended.

2. Perform the setup procedure.

   ```
   $ cargo run --features unstable-msc4295 -- setup --data=/path/to/database
   Matrix homeserver: <HOMESERVER>
   User name: <USERNAME>
   Password: <PASSWORD>
   ```

   Depending on whether a backup exists on the server, you may be asked:
   ```
   Backup recovery key: <RECOVERY KEY>
   ```
   Or:
   ```
   Are you ready to reset the cryptographic identity to enable server-side backup (y/n)? y
   Please move <DATA PATH>/recovery-key.txt to a safe place, then press ENTER to continue:
   ```

3. Run the bot.

   ```
   $ cargo run --features unstable-msc4295 -- run --data=/path/to/database
   ```

   The database path has to match the previous step. If you want to run multiple bots, each one has to use a different database path.

   Since matrixbot-ezlogin remembers your authentication, this step requires no human interaction, and can be set to start automatically on computer bootup.

4. Remove any unverified registration session.

   The `register_new_matrix_user` program mentioned above may have created an unverified session. This session should be removed to prevent encryption problems.

   Use a Matrix client, for example, [Element](https://matrix.org/ecosystem/clients/element/), to log into your bot account. Type in your recovery key when asked.

   Go to settings. In the Sessions tab, sign out any unverified or unrecognized sessions.

   Finally, sign out of Element.

5. Chat with echo-bot.

   Echo-bot responds to every message.

   Send something to echo-bot in a DM, and see if it echoes back.

6. **(Dangerous!)** Test bot loops.

   Change `--features unstable-msc4295` to `--features allow-looping` to disable looping protection.

   Add two instances of echo-bot to a room. Send something and watch them loop with each other.

   Remember to only test it in a sandbox server and be ready to terminate the experiment when anything goes wrong!

## Disclaimer

This is a proof-of-concept of a bot loop prevention mechanism. Because of the nature of this mechanism, many safety checks included in the original `matrixbot-ezlogin/echo-bot` has to be turned off, thus the code may be misused for malicious purposes, and it is technically impossible for me to prevent such misuse while keeping the proof-of-concept effective. Please do not use the software in any irresponsible manner and I cannot hold resposibility of how you use the software!
