# rowelix
integrate [rovr](https://github.com/NSPC911/rovr), [wezterm](https://github.com/wezterm/wezterm) and [helix](https://github.com/helix-editor/helix) in a nice way

inspired by [yazelix](https://github.com/luccahuguet/yazelix), go and check that out if you use yazi and zellij

this is a super basic implementation that makes use of rovr's currently alpha feature, [`modes`](https://github.com/NSPC911/rovr/blob/dev/docs/src/content/docs/features/modes.mdx)

## get started
### requirements
this needs wezterm, rovr and helix installed obviously

currently, this supports only powershell, bash and nushell will be added if there is demand

### wezterm setup
nothing for now, just install wezterm and make sure it works

### rovr setup
go to your `config.toml` (found by running `rovr --config-path`) and add the following lines
```toml
[mode.helix]
"plugins.editor.file_executable" = "powershell -noni -nop -nol -c rovr-helix"
# -noi: no interactive
# -nop: no profile
# -nol: no logo
# -c: command to run
"plugins.editor.file_suspend" = false
# disable rovr hiding itself when opening a file
"plugins.editor.open_all_in_editor" = true
# open every file in the editor regardless of encoding
"interface.clock.enabled" = false
# hide clock
"interface.compact_mode.buttons" = true
# compact header buttons
```

#### optional rovr theme
go to your `style.tcss` and add the lines from https://github.com/NSPC911/rowelix/blob/main/style.tcss to your theme

this isn't necessary, but it makes the sidebar look especially nice

### helix setup
add this to your `config.toml`, but use whatever keybind you want
```toml
[keys.normal]
A-f = ":sh wezterm cli split-pane --left --cells 26 --cwd %{current_working_directory} rovr --mode helix"
```

nothing for now, but i will see what i can do with `--chooser-file` in the future

# script
save the following as `rovr-helix.ps1` that is accessible via `$PATH`
```pwsh
$rightPaneId = wezterm cli get-pane-direction right
if (($rightPaneId).Length -eq 0) {
  $rightPaneId = wezterm cli split-pane --right --percent 75 --cwd (get-location | select -expand path) hx
  Start-Sleep -Milliseconds 500
}
echo ":open $args" | wezterm cli send-text --pane-id $rightPaneId --no-paste
wezterm cli activate-pane --pane-id $rightPaneId
```

and thats it. you can start rovr with `rovr --mode helix` and everything should work as expected
