---
order: 5
title:
  zh-CN: 动态增减表单项
  en-US: Dynamic Form Item
---

## zh-CN

动态增加、减少表单项。

## en-US

Add or remove form items dynamically.

```jsx
import React from 'react';
import { Form, Input, Icon, Button } from 'antd';

class DynamicFieldSet extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      defaultData: [],
    };
  }

  componentDidMount() {
    // ajax
    const defaultData = {
      names: [
        { id: 1, value: 'a' },
        { id: 2, value: 'b' },
      ],
    };
    this.setState({ defaultData });
  }

  remove = i => {
    const defaultData = this.state.defaultData || { names: [] };
    defaultData.names = defaultData.names.filter((_, index) => index !== i);
    this.setState({ defaultData });
  };

  add = () => {
    const defaultData = this.state.defaultData || { names: [] };
    defaultData.names.push({ key: new Date().getTime() });
    this.setState({ defaultData });
  };

  handleSubmit = e => {
    e.preventDefault();
    this.props.form.validateFields((err, values) => {
      if (!err) {
        console.log('Received values of form: ', values);
        // 要想保留names[]内的id，使用defaultData
        console.log('Edit result data:', this.state.defaultData.names);
      }
    });
  };

  onChange = (i, e) => {
    const defaultData = this.state.defaultData;
    defaultData.names[i].value = e.target.value;
    this.setState({ defaultData });
  };

  render() {
    const { getFieldDecorator } = this.props.form;
    const formItemLayout = {
      labelCol: {
        xs: { span: 24 },
        sm: { span: 4 },
      },
      wrapperCol: {
        xs: { span: 24 },
        sm: { span: 20 },
      },
    };
    const formItemLayoutWithOutLabel = {
      wrapperCol: {
        xs: { span: 24, offset: 0 },
        sm: { span: 20, offset: 4 },
      },
    };
    const names = this.state.defaultData.names;
    const formItems =
      Array.isArray(names) &&
      names.map((k, index) => (
        <Form.Item
          {...(index === 0 ? formItemLayout : formItemLayoutWithOutLabel)}
          label={index === 0 ? 'Passengers' : ''}
          required={false}
          key={k.id || k.key}
        >
          {getFieldDecorator(`names[${index}].value`, {
            validateTrigger: ['onChange', 'onBlur'],
            initialValue: k.value || '',
            rules: [
              {
                required: true,
                whitespace: true,
                message: "Please input passenger's name or delete this field.",
              },
            ],
          })(
            <Input
              placeholder="passenger name"
              style={{ width: '60%', marginRight: 8 }}
              onChange={this.onChange.bind(this, index)}
            />,
          )}
          {names.length > 1 ? (
            <Icon
              className="dynamic-delete-button"
              type="minus-circle-o"
              onClick={this.remove.bind(this, index)}
            />
          ) : null}
        </Form.Item>
      ));
    return (
      <Form onSubmit={this.handleSubmit}>
        {formItems}
        <Form.Item {...formItemLayoutWithOutLabel}>
          <Button type="dashed" onClick={this.add} style={{ width: '60%' }}>
            <Icon type="plus" /> Add field
          </Button>
        </Form.Item>
        <Form.Item {...formItemLayoutWithOutLabel}>
          <Button type="primary" htmlType="submit">
            Submit
          </Button>
        </Form.Item>
      </Form>
    );
  }
}

const WrappedDynamicFieldSet = Form.create({ name: 'dynamic_form_item' })(DynamicFieldSet);
ReactDOM.render(<WrappedDynamicFieldSet />, mountNode);
```

```css
.dynamic-delete-button {
  position: relative;
  top: 4px;
  color: #999;
  font-size: 24px;
  cursor: pointer;
  transition: all 0.3s;
}
.dynamic-delete-button:hover {
  color: #777;
}
.dynamic-delete-button[disabled] {
  cursor: not-allowed;
  opacity: 0.5;
}
```
