Title: Typer file autocompletion workaround
Date: 2022-05-07 16:36
Modified: 2022-05-08 20:14
Tags: Python
Authors: ubaumann

I like [Typer](https://typer.tiangolo.com/)'s "autocompletion", but it looks like there is a problem in the underlying lib "click-completion" with file path completion.

So I wrote a workaround. Maybe someone knows a better solution, but so far it works fine.

![code sample with auto-complete for typer]({static}/images/202205_typer_autocompletion.jpg)

[Example usage in ``nettowel``](https://github.com/InfrastructureAsCode-ch/nettowel/blob/d569122472bf1903c06708e266f8deb6ba5947ba/nettowel/cli/_common.py#L43).
