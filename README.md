```

import React from 'react';
import { shallow } from 'enzyme';
import PaymentInformationScreen from '../PaymentInformationScreen';

describe('PaymentInformationScreen', () => {
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
      paymentInformation: {
        body: {
          SECURE: {
            voidChequeAccountDetails: {
              accountName: 'Test Name',
              accountNumber: '123456',
              currency: 'USD',
              branchTransitNumber: '001',
              financialInstitutionNumber: '123',
              recipientName: 'John',
              recipientAddress: '123 St',
              recipientBank: 'BMO',
              swiftBic: 'BMO123',
              abaRoutingNumber: '011000138',
              correspondentBank: 'JPM',
              correspondentSwiftBic: 'JPM456',
            },
          },
        },
      },
    },
  });

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
    ).at(1); // second button with getVoidChequeData
    button.prop('onPress')();
    expect(props.getVoidChequeData).toHaveBeenCalled();
  });

  test('renders MECH payment instructions when accountSource is MECH', () => {
    const rendered = shallow(wrapper.instance().renderPaymentInstructions());
    expect(rendered.text()).toContain('VOID_CHEQUE.PaymentInstructions');
    expect(rendered.find('PaymentInformationItem').length).toBeGreaterThan(0);
  });

  test('renders XIM payment instructions when accountSource is XIM', () => {
    const ximProps = getBaseProps('XIM');
    const ximWrapper = shallow(<PaymentInformationScreen {...ximProps} />);
    const rendered = shallow(ximWrapper.instance().renderPaymentInstructions());
    expect(rendered.text()).toContain('VOID_CHEQUE.PaymentInstructions');
    expect(rendered.find('PaymentInformationItem').length).toBeGreaterThan(0);
  });
});


```
