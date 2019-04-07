---
title: Phone Number Primitive
author: chaospandion
type: post
date: 2010-03-17T15:03:13+00:00
url: /index.php/desktopdev/mstech/phone-number-primitive/
views:
  - 3660
rp4wp_auto_linked:
  - 1
categories:
  - 'C#'
  - Microsoft Technologies
tags:
  - .net
  - 'c#'
  - primitves

---
I don&#8217;t know how many times I&#8217;ve put together the same slow regexp to check if the user input is a valid phone number. I decided that this cannot go on and developed a simple phone number primitive. Now our main concern here is the performance of TryParse since it will be used the most. After running some performance tests the median timing was 400ns on a 3Ghz machine.

<pre>[Serializable]
public struct PhoneNumber : IEquatable<PhoneNumber>
{
    private const int AreaCodeShift = 54;
    private const int CentralOfficeCodeShift = 44;
    private const int SubscriberNumberShift = 30;
    private const int CentralOfficeCodeMask = 0x000003FF;
    private const int SubscriberNumberMask = 0x00003FFF;
    private const int ExtensionMask = 0x3FFFFFFF;


    private readonly ulong value;


    public int AreaCode
    {
        get { return UnmaskAreaCode(value); }
    }

    public int CentralOfficeCode
    {
        get { return UnmaskCentralOfficeCode(value); }
    }

    public int SubscriberNumber
    {
        get { return UnmaskSubscriberNumber(value); }
    }

    public int Extension
    {
        get { return UnmaskExtension(value); }
    }


    public PhoneNumber(ulong value)
        : this(UnmaskAreaCode(value), UnmaskCentralOfficeCode(value), UnmaskSubscriberNumber(value), UnmaskExtension(value), true)
    {

    }

    public PhoneNumber(int areaCode, int centralOfficeCode, int subscriberNumber)
        : this(areaCode, centralOfficeCode, subscriberNumber, 0, true)
    {

    }

    public PhoneNumber(int areaCode, int centralOfficeCode, int subscriberNumber, int extension)
        : this(areaCode, centralOfficeCode, subscriberNumber, extension, true)
    {

    }

    private PhoneNumber(int areaCode, int centralOfficeCode, int subscriberNumber, int extension, bool throwException)
    {
        value = 0;

        if (areaCode < 200 || areaCode > 989)
        {
            if (!throwException) return;
            throw new ArgumentOutOfRangeException("areaCode", areaCode, @"The area code portion must fall between 200 and 989.");
        }
        else if (centralOfficeCode < 200 || centralOfficeCode > 999)
        {
            if (!throwException) return;
            throw new ArgumentOutOfRangeException("centralOfficeCode", centralOfficeCode, @"The central office code portion must fall between 200 and 999.");
        }
        else if (subscriberNumber < 0 || subscriberNumber > 9999)
        {
            if (!throwException) return;
            throw new ArgumentOutOfRangeException("subscriberNumber", subscriberNumber, @"The subscriber number portion must fall between 0 and 9999.");
        }
        else if (extension < 0 || extension > 1073741824)
        {
            if (!throwException) return;
            throw new ArgumentOutOfRangeException("extension", extension, @"The extension portion must fall between 0 and 1073741824.");
        }
        else if (areaCode.ToString()[1] - 48 > 8)
        {
            if (!throwException) return;
            throw new ArgumentOutOfRangeException("areaCode", areaCode, @"The second digit of the area code cannot be greater than 8.");
        }
        else
        {
            value |= ((ulong)(uint)areaCode << AreaCodeShift);
            value |= ((ulong)(uint)centralOfficeCode << CentralOfficeCodeShift);
            value |= ((ulong)(uint)subscriberNumber << SubscriberNumberShift);
            value |= ((ulong)(uint)extension);
        }
    }

    public override bool Equals(object obj)
    {
        return obj != null && obj.GetType() == typeof(PhoneNumber) && Equals((PhoneNumber)obj);
    }

    public bool Equals(PhoneNumber other)
    {
        return this.value == other.value;
    }

    public override int GetHashCode()
    {
        return value.GetHashCode();
    }

    public override string ToString()
    {
        return ToString(PhoneNumberFormat.Separated);
    }

    public string ToString(PhoneNumberFormat format)
    {
        switch (format)
        {
            case PhoneNumberFormat.Plain:
                return string.Format(@"{0:D3}{1:D3}{2:D4} {3:#}", AreaCode, CentralOfficeCode, SubscriberNumber, Extension).Trim();
            case PhoneNumberFormat.Separated:
                return string.Format(@"{0:D3}-{1:D3}-{2:D4} {3:#}", AreaCode, CentralOfficeCode, SubscriberNumber, Extension).Trim();
            default:
                throw new ArgumentOutOfRangeException("format");
        }
    }

    public ulong ToUInt64()
    {
        return value;
    }


    public static PhoneNumber Parse(string value)
    {
        var result = default(PhoneNumber);
        if (!TryParse(value, out result))
        {
            throw new FormatException(string.Format(@"The string ""{0}"" could not be parsed as a phone number.", value));
        }
        return result;
    }

    public static bool TryParse(string value, out PhoneNumber result)
    {
        result = default(PhoneNumber);

        if (string.IsNullOrEmpty(value))
        {
            return false;
        }

        var index = 0;
        var numericPieces = new char[value.Length];

        foreach (var c in value)
        {
            if (char.IsNumber(c))
            {
                numericPieces[index++] = c;
            }
        }

        if (index < 9)
        {
            return false;
        }

        var numericString = new string(numericPieces);
        var areaCode = int.Parse(numericString.Substring(0, 3));
        var centralOfficeCode = int.Parse(numericString.Substring(3, 3));
        var subscriberNumber = int.Parse(numericString.Substring(6, 4));
        var extension = 0;

        if (numericString.Length > 10)
        {
            extension = int.Parse(numericString.Substring(10));
        }

        result = new PhoneNumber(
            areaCode,
            centralOfficeCode,
            subscriberNumber,
            extension,
            false
        );

        return result.value != 0;
    }

    public static bool operator ==(PhoneNumber left, PhoneNumber right)
    {
        return left.Equals(right);
    }

    public static bool operator !=(PhoneNumber left, PhoneNumber right)
    {
        return !left.Equals(right);
    }

    private static int UnmaskAreaCode(ulong value)
    {
        return (int)(value >> AreaCodeShift);
    }

    private static int UnmaskCentralOfficeCode(ulong value)
    {
        return (int)((value >> CentralOfficeCodeShift) & CentralOfficeCodeMask);
    }

    private static int UnmaskSubscriberNumber(ulong value)
    {
        return (int)((value >> SubscriberNumberShift) & SubscriberNumberMask);
    }

    private static int UnmaskExtension(ulong value)
    {
        return (int)(value & ExtensionMask);
    }
}

public enum PhoneNumberFormat
{
    Plain,
    Separated
}</pre>