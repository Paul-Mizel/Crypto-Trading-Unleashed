

enableCustom = input.bool(false, '', group='Custom External Indicator', inline='custom')
customName = input.string('Custom', '', group='Custom External Indicator', inline='custom')
customValue = input.source(close, '', group='Custom External Indicator', inline='custom', tooltip = 'Enable and configure custom external indicator.')