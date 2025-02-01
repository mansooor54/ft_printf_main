# ft_printf_main

https://github.com/Surfi89/ft_printf.git


Step-by-Step Guide to Writing ft_printf (Custom printf Implementation in C)

ft_printf is a custom implementation of the standard printf function, commonly used in C programming. It allows formatted output using format specifiers like %d, %s, %c, etc.

Step 1: Understand the Function Signature

Before writing ft_printf, let’s analyze what the standard printf function does:

int printf(const char *format, ...);

	•	It takes a format string (const char *format).
	•	It accepts variable arguments (...).
	•	It returns the number of characters printed.

Similarly, ft_printf should have:

int ft_printf(const char *format, ...);

Step 2: Create the Base Function

We’ll start by including necessary headers and defining the base function.

#include <stdarg.h>   // For handling variable arguments
#include <unistd.h>   // For write() function

int ft_printf(const char *format, ...);

Step 3: Handle Characters & Strings

We need a helper function to print characters and strings since write() is used for output.

Helper Function 1: Print a Character

int ft_putchar(char c)
{
    return write(1, &c, 1);  // Write character 'c' to stdout
}

	•	The write(1, &c, 1) function writes 1 byte (char) to the standard output (1).
	•	Returns 1 to count printed characters.

Helper Function 2: Print a String

int ft_putstr(char *str)
{
    int i = 0;

    if (!str)
        return write(1, "(null)", 6);  // Handle NULL strings
    while (str[i])
        ft_putchar(str[i++]);  // Print each character
    return i;  // Return string length
}

	•	Loops through str and prints each character using ft_putchar.
	•	Returns the total number of printed characters.
	•	Handles NULL by printing "(null)".

Step 4: Handle Integer Printing

Helper Function 3: Print an Integer

int ft_putnbr(int n)
{
    int count = 0;
    char num;

    if (n == -2147483648)  // Handle INT_MIN explicitly
        return write(1, "-2147483648", 11);
    if (n < 0)
    {
        count += ft_putchar('-');
        n = -n;
    }
    if (n >= 10)
        count += ft_putnbr(n / 10);  // Recursive call to print the first digits
    num = (n % 10) + '0';  // Convert last digit to char
    count += ft_putchar(num);
    
    return count;
}

	•	Handles negative numbers and recursion for printing digits.
	•	Uses write() to output characters one by one.

Step 5: Implement Format Specifier Parsing
	•	We need to iterate through the format string and handle specifiers like %d, %s, etc.

int ft_printf(const char *format, ...)
{
    va_list args; // Argument list
    int i = 0;
    int count = 0;

    va_start(args, format); // Initialize the argument list

    while (format[i])
    {
        if (format[i] == '%' && format[i + 1]) // Check for format specifier
        {
            i++;
            if (format[i] == 'c')
                count += ft_putchar(va_arg(args, int)); // Print character
            else if (format[i] == 's')
                count += ft_putstr(va_arg(args, char *)); // Print string
            else if (format[i] == 'd' || format[i] == 'i')
                count += ft_putnbr(va_arg(args, int)); // Print integer
            else
                count += ft_putchar(format[i]); // Handle invalid specifier as normal character
        }
        else
            count += ft_putchar(format[i]); // Print normal characters
        i++;
    }
    va_end(args); // Cleanup argument list
    return count; // Return total characters printed
}

Breakdown:
	1.	Initialize va_list and va_start(args, format); to process variable arguments.
	2.	Loop through format string:
	•	If a format specifier (%) is found:
	•	Check the next character to determine which type (%c, %s, %d).
	•	Call the corresponding function (ft_putchar, ft_putstr, ft_putnbr).
	•	If no format specifier, print the character as is.
	3.	Return total printed characters.

Step 6: Test ft_printf

Compile the program and test with different format specifiers:

#include <stdio.h>

int main()
{
    ft_printf("Hello %s!\n", "World");        // Hello World!
    ft_printf("Character: %c\n", 'A');        // Character: A
    ft_printf("Number: %d\n", 42);            // Number: 42
    ft_printf("Negative: %d\n", -1234);       // Negative: -1234
    return 0;
}

Expected Output:

Hello World!
Character: A
Number: 42
Negative: -1234

Step 7: Extend to Support Hexadecimal & Unsigned Numbers

Helper Function 4: Print Unsigned Integer

int ft_putnbr_unsigned(unsigned int n)
{
    int count = 0;
    char num;

    if (n >= 10)
        count += ft_putnbr_unsigned(n / 10);
    num = (n % 10) + '0';
    count += ft_putchar(num);
    
    return count;
}

Helper Function 5: Print Hexadecimal

int ft_puthex(unsigned int n, char format)
{
    int count = 0;
    char *base;

    if (format == 'x')
        base = "0123456789abcdef";
    else
        base = "0123456789ABCDEF";

    if (n >= 16)
        count += ft_puthex(n / 16, format);
    count += ft_putchar(base[n % 16]);

    return count;
}

Update ft_printf to Handle %u, %x, %X

else if (format[i] == 'u')
    count += ft_putnbr_unsigned(va_arg(args, unsigned int));
else if (format[i] == 'x' || format[i] == 'X')
    count += ft_puthex(va_arg(args, unsigned int), format[i]);

Final Test Cases

ft_printf("Unsigned: %u\n", 4294967295);  // Unsigned: 4294967295
ft_printf("Hex Lower: %x\n", 255);        // Hex Lower: ff
ft_printf("Hex Upper: %X\n", 255);        // Hex Upper: FF

Conclusion

We’ve successfully built a custom printf (ft_printf) in C. Key takeaways:
	•	Used write() for low-level character output.
	•	Handled char, string, integer, unsigned, and hexadecimal printing.
	•	Used va_list to manage variable arguments.
	•	Ensured correct return values for character counting.

This is a great exercise for deepening C skills, working with low-level I/O, and understanding function variadic arguments.
