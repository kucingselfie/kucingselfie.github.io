---
title: "Flutter: Membuat Custom Form Yang Kekinian"
tags: [flutter]
comments: true
---

Apa itu form ? Form adalah widget di Flutter yang biasanya dipakai untuk inputan data seperti nama, ktp, alamat, gender dll. Seperti ini contohnya

<img width="407" alt="Screen Shot 2022-11-17 at 00 55 25" src="https://user-images.githubusercontent.com/47325506/202258615-5875c12f-235c-478a-8ae7-9c001ab5e0db.png">

Untuk cara buatnya sendiri terbilang cukup mudah, teman-teman hanya perlu mengerti jenis inputan (TextField) dan atribut TextField tersebut. Pada gambar diatas terdapat beberapa input yaitu Free Text, Date, Phone dan Dropdown.
Mari kita coba buat satu persatu.

Pertama siapin stylenya dulu

```
import 'package:flutter/material.dart';
import '../utils/extensions/context_extension.dart';
import 'custom_color.dart';

mixin StyleGuideMixin {
  ThemeData themeData(BuildContext context) {
    return context.theme;
  }

  TextTheme textTheme(BuildContext context) {
    return context.theme.textTheme;
  }

  TextStyle h1(BuildContext context) {
    return context.textTheme.headlineLarge!.copyWith(
      fontSize: 36,
      fontWeight: FontWeight.w500,
      letterSpacing: 0.35,
      color: Colors.black
    );
  }

  TextStyle h2(BuildContext context) {
    return context.textTheme.headlineMedium!.copyWith(
      fontSize: 28,
      fontWeight: FontWeight.w500,
      letterSpacing: 0.35,
    );
  }

  TextStyle h3(BuildContext context) {
    return context.textTheme.headlineSmall!.copyWith(
      fontSize: 20,
      fontWeight: FontWeight.w500,
      letterSpacing: 0.35,
    );
  }

  TextStyle labelLarge(BuildContext context) {
    return context.textTheme.labelLarge!.copyWith(
      fontSize: 16,
      fontWeight: FontWeight.w400,
      letterSpacing: 0.1
    );
  }

  TextStyle labelMedium(BuildContext context) {
    return context.textTheme.labelMedium!.copyWith(
      fontSize: 14,
      fontWeight: FontWeight.w400,
      letterSpacing: 0.1,
      color: CustomColor.text,
    );
  }

  TextStyle labelSmall(BuildContext context) {
    return context.textTheme.labelSmall!.copyWith(
      fontSize: 12,
      fontWeight: FontWeight.w400,
      letterSpacing: 0.1,
      color: CustomColor.text,
    );
  }
}
```

## Free Text

```
import 'package:flutter/material.dart';
import 'package:flutter/services.dart';

import '../../style_guide/custom_color.dart';
import '../../style_guide/style_guide_mixin.dart';
import '../../utils/constants/constant_value.dart';

class CustomTextField extends StatelessWidget with StyleGuideMixin {
  final String hintText;
  final String? labelText;
  final String? Function(String?)? validator;
  final TextEditingController? controller;
  final AutovalidateMode autovalidateMode;
  final FocusNode? focusNode;
  final bool autoFocus;
  final VoidCallback? onEditingComplete;
  final VoidCallback? onTap;
  final TextInputType? textInputType;
  final double? borderRadius;
  final int? maxLines;
  final int? maxLength;
  final bool readOnly;
  final bool disabled;
  final Widget? icon;
  final bool obscureText;
  final TextInputAction? textInputAction;
  final String? errorText;
  final bool isRequired;
  final List<TextInputFormatter>? inputFormatters;
  final Widget? prefixIcon;
  final String? prefixText;

  const CustomTextField({
    Key? key,
    this.labelText,
    this.onTap,
    this.validator,
    this.hintText = "",
    this.focusNode,
    this.autoFocus = false,
    this.maxLines,
    this.maxLength,
    this.readOnly = false,
    this.borderRadius,
    this.textInputType,
    this.onEditingComplete,
    this.controller,
    this.autovalidateMode = AutovalidateMode.onUserInteraction,
    this.disabled = false,
    this.icon,
    this.obscureText = false,
    this.textInputAction,
    this.errorText,
    this.isRequired = false,
    this.inputFormatters,
    this.prefixIcon,
    this.prefixText
  }) : super(key: key);

  InputBorder? get _border {
    return OutlineInputBorder(
      borderSide: const BorderSide(
        width: 1,
        color: CustomColor.inactive,
      ),
      borderRadius: BorderRadius.circular(
        borderRadius ?? ConstantValue.inputBorderRadius,
      ),
    );
  }

  List<TextInputFormatter>? get customInputFormatters {
    if (textInputType == CustomTextInputType.float) {
      return [
        FilteringTextInputFormatter.allow(RegExp(r'^\d*\.?\d{0,10}')),
      ];
    }

    if (textInputType == CustomTextInputType.integer) {
      return [
        FilteringTextInputFormatter.digitsOnly,
      ];
    }

    return null;
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        _buildLabel(context),
        TextFormField(
          onTap: onTap,
          readOnly: readOnly,
          autofocus: autoFocus,
          focusNode: focusNode,
          autocorrect: false,
          maxLength: maxLength,
          validator: validator,
          maxLines: obscureText ? 1 : maxLines,
          keyboardType: textInputType,
          controller: controller,
          onEditingComplete: onEditingComplete,
          textInputAction: textInputAction,
          enabled: !disabled,
          obscureText: obscureText,
          inputFormatters: inputFormatters ?? customInputFormatters,
          decoration: InputDecoration(
            hintText: hintText,
            errorText: errorText,
            contentPadding: const EdgeInsets.symmetric(
              vertical: 20,
              horizontal: 16,
            ),
            suffixIcon: icon,
            errorStyle: labelMedium(context).copyWith(
              color: CustomColor.error,
            ),
            prefixIcon: prefixIcon,
            hintStyle: labelLarge(context).copyWith(
              color: CustomColor.inputText,
            ),
            fillColor: disabled ? CustomColor.inactive : Colors.white,
            filled: true,
            enabledBorder: _border,
            disabledBorder: _border,
            focusedBorder: _border,
            border: _border,
            prefixText: prefixText,
          ),
        ),
      ],
    );
  }

  Widget _buildLabel(BuildContext context) {
    if (labelText == null) {
      return const SizedBox();
    }

    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        Container(
          margin: const EdgeInsets.only(left: 4),
          child: Row(
            children: [
              Text(
                labelText!,
                style: labelLarge(context),
              ),
              isRequired
                  ? Container(
                margin: const EdgeInsets.only(left: 2),
                child: Text(
                  "*",
                  style: labelLarge(context).copyWith(
                    color: CustomColor.error,
                    fontWeight: FontWeight.bold,
                  ),
                ),
              )
                  : const SizedBox()
            ],
          ),
        ),
        const SizedBox(height: 15),
      ],
    );
  }
}

class CustomTextInputType extends TextInputType {
  const CustomTextInputType.numberWithOptions() : super.numberWithOptions();

  static const TextInputType float = TextInputType.numberWithOptions(
    decimal: true,
  );

  static const TextInputType integer = TextInputType.number;
}

```

Dan cara pakainya adalah

```
 const CustomTextField(
                labelText: 'Lokasi Kunjungan',
                hintText: 'Misal: lapangan',
              )
```

<img width="399" alt="Screen Shot 2022-11-17 at 01 43 59" src="https://user-images.githubusercontent.com/47325506/202266438-f0222deb-fa9a-4e93-a97d-bf6da6ac28bd.png">

## Date

```
import 'package:flutter/material.dart';
import 'package:flutter/services.dart';
import 'package:flutter_svg/svg.dart';

import '../../../style_guide/custom_color.dart';
import '../../../style_guide/style_guide_mixin.dart';
import '../../../utils/constants/constant_asset.dart';
import '../../../utils/constants/constant_value.dart';

class CustomDateField extends StatelessWidget with StyleGuideMixin {
  final String hintText;
  final String? labelText;
  final String? Function(String?)? validator;
  final TextEditingController? controller;
  final AutovalidateMode autovalidateMode;
  final FocusNode? focusNode;
  final bool autoFocus;
  final VoidCallback? onEditingComplete;
  final VoidCallback? onTap;
  final TextInputType? textInputType;
  final double? borderRadius;
  final int? maxLines;
  final int? maxLength;
  final bool readOnly;
  final bool disabled;
  final Widget? icon;
  final bool obscureText;
  final TextInputAction? textInputAction;
  final String? errorText;
  final bool isRequired;
  final List<TextInputFormatter>? inputFormatters;
  final Widget? prefixIcon;
  final String? prefixText;

  const CustomDateField({
    Key? key,
    this.labelText,
    this.onTap,
    this.validator,
    this.hintText = "",
    this.focusNode,
    this.autoFocus = false,
    this.maxLines,
    this.maxLength,
    this.readOnly = false,
    this.borderRadius,
    this.textInputType,
    this.onEditingComplete,
    this.controller,
    this.autovalidateMode = AutovalidateMode.onUserInteraction,
    this.disabled = false,
    this.icon,
    this.obscureText = false,
    this.textInputAction,
    this.errorText,
    this.isRequired = false,
    this.inputFormatters,
    this.prefixIcon,
    this.prefixText
  }) : super(key: key);

  InputBorder? get _border {
    return const OutlineInputBorder(
      borderSide: BorderSide(
        width: 1,
        color: CustomColor.inactive,
      ),
      borderRadius: BorderRadius.only(
        topLeft: Radius.circular(ConstantValue.inputBorderRadius),
        bottomLeft: Radius.circular(ConstantValue.inputBorderRadius),
      ),
    );
  }

  List<TextInputFormatter>? get customInputFormatters {
    if (textInputType == CustomTextInputType.float) {
      return [
        FilteringTextInputFormatter.allow(RegExp(r'^\d*\.?\d{0,10}')),
      ];
    }

    if (textInputType == CustomTextInputType.integer) {
      return [
        FilteringTextInputFormatter.digitsOnly,
      ];
    }

    return null;
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        _buildLabel(context),
        Row(
          children: [
            Expanded(
              flex: 5,
              child: TextFormField(
                onTap: onTap,
                readOnly: readOnly,
                autofocus: autoFocus,
                focusNode: focusNode,
                autocorrect: false,
                maxLength: maxLength,
                validator: validator,
                maxLines: obscureText ? 1 : maxLines,
                keyboardType: TextInputType.number,
                controller: controller,
                onEditingComplete: onEditingComplete,
                textInputAction: textInputAction,
                enabled: !disabled,
                obscureText: obscureText,
                inputFormatters: [FilteringTextInputFormatter.digitsOnly],
                decoration: InputDecoration(
                  hintText: hintText,
                  errorText: errorText,
                  contentPadding: const EdgeInsets.symmetric(
                    vertical: 20,
                    horizontal: 16,
                  ),
                  suffixIcon: icon,
                  errorStyle: labelMedium(context).copyWith(
                    color: CustomColor.error,
                  ),
                  prefixIcon: prefixIcon,
                  hintStyle: labelLarge(context).copyWith(
                    color: CustomColor.inputText,
                  ),
                  fillColor: disabled ? CustomColor.inactive : Colors.white,
                  filled: true,
                  enabledBorder: _border,
                  disabledBorder: _border,
                  focusedBorder: _border,
                  border: _border,
                  prefixText: prefixText,
                ),
              ),
            ),
            Expanded(flex: 1, child: Container(
                padding: const EdgeInsets.symmetric(vertical: 22, horizontal: 16),
                decoration: BoxDecoration(
                    borderRadius: const BorderRadius.only(topRight: Radius.circular(10), bottomRight: Radius.circular(10)),
                    color: CustomColor.secondary, border: Border.all(color: CustomColor.inactive)
                ),
                child: SvgPicture.asset(
                  ConstantAsset.icCalendar,
                  color: Colors.white,
                  width: 20,
                  height: 20,
                ),
            )),
          ],
        ),
      ],
    );
  }

  Widget _buildLabel(BuildContext context) {
    if (labelText == null) {
      return const SizedBox();
    }

    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        Container(
          margin: const EdgeInsets.only(left: 4),
          child: Row(
            children: [
              Text(
                labelText!,
                style: labelLarge(context),
              ),
              isRequired
                  ? Container(
                margin: const EdgeInsets.only(left: 2),
                child: Text(
                  "*",
                  style: labelLarge(context).copyWith(
                    color: CustomColor.error,
                    fontWeight: FontWeight.bold,
                  ),
                ),
              )
                  : const SizedBox()
            ],
          ),
        ),
        const SizedBox(height: 15),
      ],
    );
  }
}

class CustomTextInputType extends TextInputType {
  const CustomTextInputType.numberWithOptions() : super.numberWithOptions();

  static const TextInputType float = TextInputType.numberWithOptions(
    decimal: true,
  );

  static const TextInputType integer = TextInputType.number;
}
```

Cara pakenya ygy, jangan lupa tambahkan fungsi untuk mengambil output dari tanggalnya

```
TextEditingController dateController = TextEditingController();
DateTime selectedDate = DateTime.now();


CustomDateField(
                labelText: 'Tanggal Kunjungan',
                hintText: 'Tanggal Kunjungan',
                readOnly: true,
                controller: dateController,
                onTap: () {
                  _selectDate(context);
                },
              )


void _selectDate(BuildContext context) async {
    final DateTime? picked = await showDatePicker(
        context: context,
        initialDate: selectedDate,
        firstDate: DateTime(1900),
        lastDate: DateTime.now(),
        builder: (context, child) {
          return Theme(
            data: ThemeData.light().copyWith(
              colorScheme: const ColorScheme.light(
                  primary: CustomColor.secondary,
                  secondary: CustomColor.secondary),
            ),
            child: child!,
          );
        });
    if (picked != null && picked != selectedDate) {
      setState(() {
        selectedDate = picked;
        var formattedDate = DateFormat('dd-MM-yyyy').format(selectedDate);
        dateController.text = formattedDate;
      });
    }
  }
```

<img width="401" alt="Screen Shot 2022-11-17 at 01 44 16" src="https://user-images.githubusercontent.com/47325506/202266540-966b704b-c55c-4f19-83f2-572873ad6991.png">


### Phone

```
import 'package:flutter/material.dart';
import 'package:flutter/services.dart';

import '../../../style_guide/custom_color.dart';
import '../../../style_guide/style_guide_mixin.dart';
import '../../../utils/constants/constant_value.dart';

class CustomPhoneField extends StatelessWidget with StyleGuideMixin {
  final String hintText;
  final String? labelText;
  final String? Function(String?)? validator;
  final TextEditingController? controller;
  final AutovalidateMode autovalidateMode;
  final FocusNode? focusNode;
  final bool autoFocus;
  final VoidCallback? onEditingComplete;
  final VoidCallback? onTap;
  final TextInputType? textInputType;
  final double? borderRadius;
  final int? maxLines;
  final int? maxLength;
  final bool readOnly;
  final bool disabled;
  final Widget? icon;
  final bool obscureText;
  final TextInputAction? textInputAction;
  final String? errorText;
  final bool isRequired;
  final List<TextInputFormatter>? inputFormatters;
  final Widget? prefixIcon;
  final String? prefixText;

  const CustomPhoneField({
    Key? key,
    this.labelText,
    this.onTap,
    this.validator,
    this.hintText = "",
    this.focusNode,
    this.autoFocus = false,
    this.maxLines,
    this.maxLength,
    this.readOnly = false,
    this.borderRadius,
    this.textInputType,
    this.onEditingComplete,
    this.controller,
    this.autovalidateMode = AutovalidateMode.onUserInteraction,
    this.disabled = false,
    this.icon,
    this.obscureText = false,
    this.textInputAction,
    this.errorText,
    this.isRequired = false,
    this.inputFormatters,
    this.prefixIcon,
    this.prefixText
  }) : super(key: key);

  InputBorder? get _border {
    return const OutlineInputBorder(
      borderSide: BorderSide(
        width: 1,
        color: CustomColor.inactive,
      ),
      borderRadius: BorderRadius.only(
        topRight: Radius.circular(ConstantValue.inputBorderRadius),
        bottomRight: Radius.circular(ConstantValue.inputBorderRadius),
      ),
    );
  }

  List<TextInputFormatter>? get customInputFormatters {
    if (textInputType == CustomTextInputType.float) {
      return [
        FilteringTextInputFormatter.allow(RegExp(r'^\d*\.?\d{0,10}')),
      ];
    }

    if (textInputType == CustomTextInputType.integer) {
      return [
        FilteringTextInputFormatter.digitsOnly,
      ];
    }

    return null;
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        _buildLabel(context),
        Row(
          children: [
            Expanded(flex: 1, child: Container(
              padding: const EdgeInsets.symmetric(vertical: 19, horizontal: 16),
                decoration: BoxDecoration(
                  borderRadius: const BorderRadius.only(topLeft: Radius.circular(10), bottomLeft: Radius.circular(10)),
                  color: Colors.white, border: Border.all(color: CustomColor.inactive)
                ),
                child: Text('+62', style: labelLarge(context),)
            )),
            Expanded(
              flex: 5,
              child: TextFormField(
                onTap: onTap,
                readOnly: readOnly,
                autofocus: autoFocus,
                focusNode: focusNode,
                autocorrect: false,
                maxLength: maxLength,
                validator: validator,
                maxLines: obscureText ? 1 : maxLines,
                keyboardType: TextInputType.number,
                controller: controller,
                onEditingComplete: onEditingComplete,
                textInputAction: textInputAction,
                enabled: !disabled,
                obscureText: obscureText,
                inputFormatters: [FilteringTextInputFormatter.digitsOnly],
                decoration: InputDecoration(
                  hintText: hintText,
                  errorText: errorText,
                  contentPadding: const EdgeInsets.symmetric(
                    vertical: 20,
                    horizontal: 16,
                  ),
                  suffixIcon: icon,
                  errorStyle: labelMedium(context).copyWith(
                    color: CustomColor.error,
                  ),
                  prefixIcon: prefixIcon,
                  hintStyle: labelLarge(context).copyWith(
                    color: CustomColor.inputText,
                  ),
                  fillColor: disabled ? CustomColor.inactive : Colors.white,
                  filled: true,
                  enabledBorder: _border,
                  disabledBorder: _border,
                  focusedBorder: _border,
                  border: _border,
                  prefixText: prefixText,
                ),
              ),
            ),
          ],
        ),
      ],
    );
  }

  Widget _buildLabel(BuildContext context) {
    if (labelText == null) {
      return const SizedBox();
    }

    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        Container(
          margin: const EdgeInsets.only(left: 4),
          child: Row(
            children: [
              Text(
                labelText!,
                style: labelLarge(context),
              ),
              isRequired
                  ? Container(
                margin: const EdgeInsets.only(left: 2),
                child: Text(
                  "*",
                  style: labelLarge(context).copyWith(
                    color: CustomColor.error,
                    fontWeight: FontWeight.bold,
                  ),
                ),
              )
                  : const SizedBox()
            ],
          ),
        ),
        const SizedBox(height: 15),
      ],
    );
  }
}

class CustomTextInputType extends TextInputType {
  const CustomTextInputType.numberWithOptions() : super.numberWithOptions();

  static const TextInputType float = TextInputType.numberWithOptions(
    decimal: true,
  );

  static const TextInputType integer = TextInputType.number;
}
```

Cara pakainya

```
const CustomPhoneField(
                labelText: 'Nomor Telepon',
                hintText: 'Nomor Telepon',
              )
```

<img width="395" alt="Screen Shot 2022-11-17 at 01 44 32" src="https://user-images.githubusercontent.com/47325506/202266694-77335abb-42f3-462d-ac89-471ceb5a04f8.png">


## Dropdown

```
import 'package:flutter/material.dart';

import '../../../style_guide/custom_color.dart';
import '../../../style_guide/style_guide_mixin.dart';
import '../../../utils/constants/constant_value.dart';

class CustomDropdownField extends StatefulWidget {
  final String? labelText;
  final bool isRequired;
  final List<String>? list;
  final Function(String)? onChanged;
  final String value;
  final String hintText;
  final String? errorText;

  const CustomDropdownField({
    Key? key,
    this.labelText,
    this.isRequired = false,
    this.list,
    this.onChanged,
    this.value = '',
    this.hintText = '',
    this.errorText
  }) : super(key: key);

  @override
  State<CustomDropdownField> createState() => _CustomDropdownFieldState();
}

class _CustomDropdownFieldState extends State<CustomDropdownField> with StyleGuideMixin {
  @override
  void initState() {
    super.initState();
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        _buildLabel(context),
        _buildDropdown()
      ],
    );
  }

  Widget _buildLabel(BuildContext context) {
    if (widget.labelText == null) {
      return const SizedBox();
    }
    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        Container(
          margin: const EdgeInsets.only(left: 4),
          child: Row(
            children: [
              Text(
                widget.labelText!,
                style: labelLarge(context),
              ),
            ],
          ),
        ),
        const SizedBox(height: 15),
      ],
    );
  }

  InputBorder? get _border {
    return OutlineInputBorder(
      borderSide: const BorderSide(
        width: 1,
        color: CustomColor.inactive,
      ),
      borderRadius: BorderRadius.circular(
        ConstantValue.inputBorderRadius,
      ),
    );
  }

  _buildDropdown() {
    return DropdownButtonFormField(
      hint: Text(widget.hintText),
      value: widget.value.isEmpty ? null : widget.value,
      icon: const Icon(Icons.keyboard_arrow_down),
      elevation: 16,
      style: const TextStyle(color: Colors.black, fontSize: 16),
      decoration: InputDecoration(
        errorText: widget.errorText,
        contentPadding: const EdgeInsets.symmetric(
          vertical: 20,
          horizontal: 16,
        ),
        errorStyle: labelMedium(context).copyWith(
          color: CustomColor.error,
        ),
        hintStyle: labelLarge(context).copyWith(
          color: CustomColor.inputText,
        ),
        fillColor: Colors.white,
        filled: true,
        enabledBorder: _border,
        disabledBorder: _border,
        focusedBorder: _border,
        border: _border,
      ),
      onChanged: (String? value) {
        if (widget.onChanged != null) {
          widget.onChanged!(value as String);
        }
      },
      items: widget.list?.map<DropdownMenuItem<String>>((String value) {
        return DropdownMenuItem<String>(
          value: value,
          child: Text(value),
        );
      }).toList(),
    );
  }
}
```

Untuk datanya tinggal teman-teman sesuaikan pada atribut `list`

```
 CustomDropdownField(
                hintText: 'Pilih Jam Kunjungan',
                list: const ['08:00', '09:00', '10:00', '11:00', '12:00', '01:00', '02:00'],
                labelText: 'Jam Kunjungan',
                onChanged: (val) {
                  // print('Value : $val');
                },
              )
```

<img width="389" alt="Screen Shot 2022-11-17 at 01 44 45" src="https://user-images.githubusercontent.com/47325506/202266774-45074ef0-5084-45a7-b49f-8d11054454f1.png">


Nah gitu aja, sekian happy coding :)


{% include disqus.html %}
