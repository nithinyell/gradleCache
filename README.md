```

const paymentInformationMECHResponse = {
  httpCode: 200,
  body: {
    SECURE: {
      voidChequeAccountDetails: {
        accountName: 'MECH INV 14',
        accountNumber: '1716119',
        branchTransitNumber: '021424',
        recipientBank: 'BMO Bank N.A Head Office, Montreal',
        correspondentBank: 'Wells Fargo Bank N.A New York',
        correspondentBankStaticText: 'PNBPUS3NNYC',
        recipientBankStaticText: 'To send wire payments in USD, use the following details.',
        currency: 'USD',
        financialInstitutionNumber: '001',
        recipientName: 'BESS ACCOUNT 17',
        recipientAddress: '123MCKINNEY ST\nTORONTO ONM 5C367 CANADA',
      },
    },
  },
};

// Mock XIM response
const paymentInformationXIMResponse = {
  httpCode: 200,
  body: {
    SECURE: {
      voidChequeAccountDetails: {
        accountName: 'XIM INV 14',
        accountNumber: '1895355',
        recipientBank: 'BMO Bank N.A.\n328 South Canal Street\nChicago, IL 60606, USA',
        abaRoutingNumber: '071000288',
        recipientBankStaticText: 'HATRUS44XXX',
        correspondentBankStaticText:
          'To send domestic or international payments to this account, please use the following details:',
        financialInstitutionNumber: '001',
        recipientName: 'ALPINE TOTAL DYNAMIC DIVIDEND FUND',
        recipientAddress: 'AAS7972 STREET (DIVISION STREET TO NORTH\nCHICAGO IL 60603',
        recipientAccountNumber: '1895355',
        swiftBic: 'HATRUS44XXX',
      },
    },
  },
};

// Helper to get props by account type
const getBaseProps = (accountSource = 'MECH') => ({
  route: {
    params: { accountSource },
  },
  navigation: {
    goBack: jest.fn(),
    navigate: jest.fn(),
  },
  getPaymentInformation: jest.fn(),
  getVoidChequeData: jest.fn(),
  voidCheque: {
    paymentInformation:
      accountSource === 'MECH' ? paymentInformationMECHResponse : paymentInformationXIMResponse,
  },
});

describe('PaymentInformationScreen', () => {
  let wrapper;
  let props;

  beforeEach(() => {
    props = getBaseProps('MECH');
    wrapper = shallow(<PaymentInformationScreen {...props} />);
  });

  test('calls getPaymentInformation on componentDidMount', () => {
    expect(props.getPaymentInformation).toHaveBeenCalledWith('MECH');
  });

  test('calls navigation.goBack on handleBack', () => {
    wrapper.instance().handleBack();
    expect(props.navigation.goBack).toHaveBeenCalled();
  });

  test('returns true for isMECHAccount when accountSource is MECH', () => {
    expect(wrapper.instance().isMECHAccount()).toBe(true);
    expect(wrapper.instance().isXIMAccount()).toBe(false);
  });

  test('returns true for isXIMAccount when accountSource is XIM', () => {
    const ximProps = getBaseProps('XIM');
    const ximWrapper = shallow(<PaymentInformationScreen {...ximProps} />);
    expect(ximWrapper.instance().isXIMAccount()).toBe(true);
    expect(ximWrapper.instance().isMECHAccount()).toBe(false);
  });

  test('renders NavigationHeader with correct title', () => {
    const header = shallow(wrapper.instance().renderPaymentInformation()).find('NavigationHeader');
    expect(header.exists()).toBe(true);
    expect(header.prop('navigationTitle')).toBe('VOID_CHEQUE.PaymentInformation');
  });

  test('calls getVoidChequeData on button press in renderFooter', () => {
    const footer = shallow(wrapper.instance().renderFooter());
    const button = footer.findWhere(
      el => el.prop('onPress') && typeof el.prop('onPress') === 'function'
    ).at(1); // 2nd button (TouchableOpacity)
    button.prop('onPress')();
    expect(props.getVoidChequeData).toHaveBeenCalled();
  });

  test('renders MECH payment instructions using actual mock data', () => {
    const rendered = shallow(wrapper.instance().renderPaymentInstructions());
    const text = rendered.text();

    expect(text).toContain('VOID_CHEQUE.PaymentInstructions');
    expect(text).toContain('MECH INV 14');
    expect(text).toContain('Wells Fargo Bank');
    expect(rendered.find('PaymentInformationItem').length).toBeGreaterThan(0);
  });

  test('renders XIM payment instructions using actual mock data', () => {
    const ximProps = getBaseProps('XIM');
    const ximWrapper = shallow(<PaymentInformationScreen {...ximProps} />);
    const rendered = shallow(ximWrapper.instance().renderPaymentInstructions());

    const text = rendered.text();
    expect(text).toContain('VOID_CHEQUE.PaymentInstructions');
    expect(text).toContain('XIM INV 14');
    expect(text).toContain('ALPINE TOTAL DYNAMIC DIVIDEND FUND');
    expect(rendered.find('PaymentInformationItem').length).toBeGreaterThan(0);
  });
});


```
